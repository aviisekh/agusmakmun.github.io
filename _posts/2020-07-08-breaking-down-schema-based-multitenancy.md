---
layout: post
title:  "Breaking down schema based multitenancy"
date:   2020-07-08 10:38:10 +0545
categories: [ruby, ror, postgresql, multitenancy, apartment, acts_as_tenant]
---

### Background
We had a SAAS platform built in Ruby on Rails, where multiple vendors could setup their account and provide their services to their own customers.
So basically, the SAAS vendor himself will be responsible to manage their customers and their services. So there was a need of proper isolation 
of the data of various vendors that provide service through our platform. 

This whole architecture was supported by the postgres schemas, where we create a separate schema for the separate vendor
and that schema contains all the necessary tables.  We used the very popular gem **apartment gem** to support the schema switching and handling the migrations. 

But in the due run, we were facing various problems with this architecure, and the major one was sharing the data across various tenants. If you want to explore 
more issues with this architecture and with using apartment gem, here is the link below:

[Journey with apartment!](https://influitive.io/our-multi-tenancy-journey-with-postgres-schemas-and-apartment-6ecda151a21f)

In this article we discuss the breakdown of postgres schema based multitenant architecture, and the achieve foreign key based multitenant architecture. 
Moreover we discuss about the challenge that we came across during this migration, and the solution to it. 

### Moving on
The whole problem is categorized into two sections. 
1. Refactoring the codebase
2. Migration of data 

#### 1. Refactoring the codebase

**apartment gem** not doubt is best suited for schema based multenant structure. But we need to get rid of this gem. 
 The new gem we will be using **acts_as_tenant gem** which serves well to manage foreing key based multitenant architecture. 
 And so the basic plan of refactoring the codebase is to remove all the dependencies of apartment gem and replace it with the 
 acts_as_tenant gem. Following are the links to their official github pages. 
* [apartment](https://github.com/influitive/apartment)
* [acts_as_tenant](https://github.com/ErwinM/acts_as_tenant)

 
#### 2. Migration of data

 This is the trickiest part of this architecture refactor. Here we need to migrate all the records from different schemas into a 
 single schema. This migration is tricky because, during migration you will realize that a record from same table (say from users table) 
 but from separate schema( say, first user from schema A, and first user from schema B ) can bear the same primary key ( ie. both with id 1). And you need to 
 transform this primary key before merging these records into same table. And the more tricky part is, this record with primary key might be referenced from 
 some other table, and you cannot simply transform this primary key, but you need to address and transform all the references to that record. 

**For an example**

If there is a foo table entry with id of 1 in many schemas and bar records have an association of foo_id of 1, 
we need to ensure the bar points to the correct foo after migration. 


### The Migration Algorithm
Let us consider **public** as the target schema, where we will be merging the records from all other schemas.
1. Add a migration that adds foreign key tenant_id in all tables except shared tables and add the respective tenant value as well. 
```ruby
 # migration to add the tenant_id in all tables
 def up
  shared_tables =  ["schema_migrations"]
  tables = ActiveRecord::Base.connection.tables.reject{|x| shared_tables.include?x}
  tenant_id = Tenant.find_by_database_name(Apartment::Tenant.current).id
  tables.each do |table|
        add_reference table, :tenant
        query = %Q(UPDATE "#{table.to_s}" SET "tenant_id" = #{tenant_id})
        execute query
  end
end
```

2. Iterating in each postgresql schema
    1. Identify all these tables' offset value. 
        
        Suppose, in users table, if public tenants' users' last id is 100.
        The offset value for users table will be 100+100(offset) = 200. 
        Similarly for all the other tables. 
    2. Iterating in each table
        1. Fetch all data of table
        2. Transform its primary id with the corresponding offset
        
            eg. User with id 1 will be the user with id (200 + 1) = 201
        3. Transform its foreign key with its foreign tables offset
        
            eg. if User contains foreign column (organization_id = 1) 
                and organizations table has offset of say (300)
                new organization id will be 300 + 1 = 301
                
        4. Transform the polymorphic columns with their respective table's offset value 
        
            eg. if notes have Polymorphic column, notable_type and notable_id
                the records with notable_type =  Activity are transformed with 
                    notable_id = notable_id + notes_offset_value
                which is if notable_id is 2 and notes offset is 200, the new notable_id will be 
                    2+200 = 202
                    
        5. Copy new the transformed values into corresponding table in the public tenant.