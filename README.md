# How to test

- `git clone git@github.com:sergey-alekseev/test-rolify.git`  
- `cd test-rolify/`  
- `bundle`  
- `cp config/database.mysql2.yml config/database.yml`  
- `rake db:create db:migrate db:seed`  
- `rails c`  
- `Role.where(name: 'Admin').explain`  
- `exit`  
- `rake db:drop`  
- comment `# gem 'mysql2'` and uncomment `gem 'sqlite3'` in `Gemfile`  
- `bundle`  
- `cp config/database.sqlite3.yml config/database.yml`  
- `rake db:create db:migrate db:seed`  
- `rails c`  
- `Role.where(name: 'Admin').explain`  
- `exit`  
- `rake db:drop`  
- comment `# gem 'sqlite3'` and uncomment `gem 'pg'` in `Gemfile`  
- `bundle`  
- `cp config/database.pg.yml config/database.yml`  
- `rake db:create db:migrate db:seed`  
- `rails c`  
- `Role.where(name: 'Admin').explain`  
- `exit`  
- `rake db:drop`  

# Results

After running `Role.where(name: 'Admin').explain` inside `rails console` with the following databases:  
- MySQL (gem 'mysql2')  
- SQLite (gem 'sqlite3')  
- PostgreSQL (gem 'pg')  

```
# mysql2
 => EXPLAIN for: SELECT `roles`.* FROM `roles` WHERE `roles`.`name` = 'Admin'
+----+-------------+-------+------------+------+---------------------------------------------------------------------------+---------------------+---------+-------+------+----------+-------+
| id | select_type | table | partitions | type | possible_keys                                                             | key                 | key_len | ref   | rows | filtered | Extra |
+----+-------------+-------+------------+------+---------------------------------------------------------------------------+---------------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | roles | NULL       | ref  | index_roles_on_name,index_roles_on_name_and_resource_type_and_resource_id | index_roles_on_name | 768     | const |    1 |    100.0 | NULL  |
+----+-------------+-------+------------+------+---------------------------------------------------------------------------+---------------------+---------+-------+------+----------+-------+
1 row in set (0.00 sec)

# sqlite3
 => EXPLAIN for: SELECT "roles".* FROM "roles" WHERE "roles"."name" = ? [["name", "Admin"]]
0|0|0|SEARCH TABLE roles USING INDEX index_roles_on_name (name=?)

# pg
 => EXPLAIN for: SELECT "roles".* FROM "roles" WHERE "roles"."name" = $1 [["name", "Admin"]]
                                                     QUERY PLAN
--------------------------------------------------------------------------------------------------------------------
 Bitmap Heap Scan on roles  (cost=4.17..11.28 rows=3 width=96)
   Recheck Cond: ((name)::text = 'Admin'::text)
   ->  Bitmap Index Scan on index_roles_on_name_and_resource_type_and_resource_id  (cost=0.00..4.17 rows=3 width=0)
         Index Cond: ((name)::text = 'Admin'::text)
(4 rows)

```
