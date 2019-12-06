---
layout: post
title:  "Ruby, Rails, Heroku and Postgresql Installation"
date:   2019-12-06 10:38:10 +0545
categories: [ruby, ror, heroku, postgresql]
---

**1. Basic Dependencies**
>
```python
1. sudo apt-get install nodejs
2. sudo apt-get install libssl1.0-dev
3. sudo apt-get -y install postgresql postgresql-contrib libpq-dev #for psql setup
4. sudo apt-get -y install libsqlite3-dev #for sqlite setup
5. sudo apt-get install imagemagick
6. sudo apt install autoconf bison build-essential libssl-dev libyaml-dev libreadline6-dev zlib1g-dev libncurses5-dev libffi-dev libgdbm5 libgdbm-dev
```

**3. Configuring Postgresql**
>
```python
1. sudo apt install postgresql postgresql-contrib
2. sudo -u postgres psql
3. alter user postgres with encrypted password 'password';\q
4. edit /etc/postgresql/10/main/pg_hba.conf file and change peer to md5
```

**3. Installing rbenv**
>
```python
1. git clone https://github.com/rbenv/rbenv.git ~/.rbenv
2. echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
3. echo 'eval "$(rbenv init -)"' >> ~/.bashrc
4. source ~/.bashrc
5. git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
```

**4. Installing Ruby and Rails**
>
```python
1. rbenv install 2.5.1
2. rbenv global 2.5.1
3. gem install bundler
4. gem install rails -v 5.2.0
```

**5. Configure Heroku CLI**
```python
1. sudo snap install --classic heroku
2. heroku login
```

**6. Create new Rails Project**
```python
rails new sweek_cakes --database=postgresql
```