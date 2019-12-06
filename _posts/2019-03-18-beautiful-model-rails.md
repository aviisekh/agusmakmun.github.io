---
layout: post
title:  "Beautiful Rails Model"
date:   2019-03-18 10:38:10 +0545
categories: [ruby, ror]
---


Apart from commenting the code or following any of the design principles, this article focuses on organizing the codes while writing a model. The basic idea is make the model beautiful.

**Disclaimer:** This is just a convention explained with absolutely no intension of criticizing on anyone else's coding style. This would however help to achieve a common coding style among the team while working in a project. 


Lets look at the example.

```ruby
class Worker < ActiveRecord::Base
  after_initialize :default_subject
  include Timeable::Preference
  has_attached_file :avatar, styles: { large: "600*600>", medium: "300*300>", small: "150*150#" },
                    s3_protocol: 'https'
  has_many :worker_resource_preferences
  has_many :preferred_resources, through: :worker_resource_preferences,
            foreign_key: :worker_id, class_name: :Vehicle
  validates_attachment_content_type :avatar, :content_type => /\Aimage/
  default_scope -> { where.not(status: Worker.statuses[:archived]) }
  enum status: { active: 0, inactive: 1, confirmation_pending: 3, verified: 4, archived: 5 }
  store :availability, accessors: [:time_available, :days_available]
  accepts_nested_attributes_for :preferred_resources
  serialize :fcm_tokens
  attr_accessor :stripe_card_token

  store :data, accessors: [:fcm_tokens, :preferred_base_id]
  scope :with_app_installed, -> { where.not(otp: nil) }
  scope :sorted, -> { order(name: :asc) }
  after_create -> { self.status = :confirmation_pending}
  include Notable::Notify
  has_many :shifts, dependent: :destroy
  has_many :tasks
  has_many :availabilities, class_name: :WorkerTimePreference
  accepts_nested_attributes_for :availabilities, allow_destroy: true
  ACTIVE_MINUTE = 2
end
```

The above code may be syntatically and semantically correct. But the code is misorganized like a hell. Let us beautify the model.

```ruby
class Worker < ActiveRecord::Base
  # section for mixins 
  include Timeable::Preference
  include Notable::Notify

  # section for attribute accessor
  attr_accessor :stripe_card_token

  # section for the constants
  ACTIVE_MINUTE = 2

  # section for enums and stores
  enum status: { active: 0, inactive: 1, confirmation_pending: 3, verified: 4, archived: 5 }
  serialize :fcm_tokens
  store :availability, accessors: [:time_available, :days_available]
  store :data, accessors: [:fcm_tokens, :preferred_base_id]

  # section for library methods
  validates_attachment_content_type :avatar, :content_type => /\Aimage/
  has_attached_file :avatar, styles: { large: "600*600>", medium: "300*300>", small: "150*150#" },
                     s3_protocol: 'https'

  # section for scopes, default scope on top
  default_scope -> { where.not(status: Worker.statuses[:archived]) }
  scope :with_app_installed, -> { where.not(otp: nil) }
  scope :sorted, -> { order(name: :asc) }

  #section for associations
  has_many :tasks
  has_many :worker_resource_preferences
  has_many :shifts, dependent: :destroy
  has_many :availabilities, class_name: :WorkerTimePreference
  has_many :preferred_resources, through: :worker_resource_preferences,
            foreign_key: :worker_id, class_name: :Vehicle

  # section for validations 
  validates :email, presence: true, uniqueness: true
  validates :phone, presence: true, uniqueness: true, allow_blank: false
  validates :name, presence: true

  # section for model callbacks
  after_initialize :default_subject
  after_create -> { self.status = :confirmation_pending}
  
  # section for nested attributes
  accepts_nested_attributes_for :preferred_resources
  accepts_nested_attributes_for :availabilities, allow_destroy: true

  # section for class methods
  def self.some_method
  end


  # section for instance methods
  def some_other_method
  end

  # section for private methods
  private
  def self.some_privete_method
  end

  def self.some_other_method
  end
end
```