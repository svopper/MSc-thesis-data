# Steps to get Libraries.io up and running

# Manual setup

> These following steps are documenting the effort of getting the Libraries.io dev enviornment up and runnning on Joakims's machine. OS: Ubuntu 20.04.3 LTS x86_64, Kernel: 5.11.0-36-generic, DE GNOME, CPU Intel i7-107000 (18) $ 4.800GHz, GPU: NVIDIA GeForce RTX 2070, Memory 1 TB

1. `git clone git@github.com:librariesio/libraries.io.git`
2. `cd ~git/libraries.io`
3. `sudo apt update`
4. `sudo apt install git curl autoconf bison build-essential \
    libssl-dev libyaml-dev libreadline6-dev zlib1g-dev \
    libncurses5-dev libffi-dev libgdbm6 libgdbm-dev libdb-dev`
5. `sudo apt install rbenv`
6. `sudo apt-get install postgresql postgresql-contriblibicu-dev`
7. `rbenv install rbx-2.6`
8. `sudo apt install redis-server`
9. `sudo systemctl start redis`
10. Setup database password and user:
	` username: <%= ENV["PG_USERNAME"] || "<librariesio>" %>
  	  password: <not-a-secret>
      host: localhost
      port: 5432 `
11. Download Elstatic search deb
	`sudo apt install ./elasticsearch-2.4.5.deb`
13. `sudo gem install bundler && rbenv rehash`
14. `sudo gem install bundler:1.17.3`
15. `bundle install`

**Can skip to step no. 37**

16. First Error:
	```Following files may not be writable, so sudo is needed:
 	 /usr/local/bin
 	 /var/lib/gems/2.7.0
 	 /var/lib/gems/2.7.0/build_info
	 /var/lib/gems/2.7.0/cache
 	 /var/lib/gems/2.7.0/doc
	 /var/lib/gems/2.7.0/extensions
	 /var/lib/gems/2.7.0/gems
 	 /var/lib/gems/2.7.0/specifications
	 Your Ruby version is 2.7.0, but your Gemfile specified 2.6.5```
17. `sudo bundle install`
18. Error:
		```Don't run Bundler as root. 
		   Bundler can ask for sudo if it is needed, and installing your bundle as root will break this application for
		   all non-root users on this machine.
		```
	 well ... guess I need to get another Ruby version, though I specifically installed 2.6.
19. ` ruby -v`
	`ruby 2.7.0p0 (2019-12-25 revision 647ee6f091) [x86_64-linux-gnu]`
20. Apparently cant install Ruby 2.6.5 with rbenv on Ubuntu.. trying rvm
21. `apt-get install gcc autoconf curl g++  gnupg automake bison libc6-dev libffi-dev libgdbm-dev libncurses5-dev libsqlite3-dev pkg-config sqlite3 zlib1g-dev libtool libyaml-dev make libgmp-dev libreadline-dev libssl-dev`
22. The appointed keyserver used to get public keys for install is deprecated. Fixed by manually trying ubuntu keyserver
23. `gpg --keyserver keyserver.ubuntu.com --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB`
24. `source /home/hey/.rvm/scripts/rvm`
25. `rvm install "ruby-2.6.5`
26. Going back to step 15
27. `bundle install` (Success, installing 800 dependencies)
28. Seems stuck at `Installing rugged 0.28.1 with native extensions`
29. Error
	```Gem::Ext::BuildError: ERROR: Failed to build gem native extension.
    current directory: /home/hey/.rvm/gems/ruby-2.6.5/gems/pg-1.1.4/ext
	/home/hey/.rvm/rubies/ruby-2.6.5/bin/ruby -I /home/hey/.rvm/rubies/ruby-2.6.5/lib/ruby/site_ruby/2.6.0 -r ./siteconf20210925-48998-1ef1dwp.rb extconf.rb
	checking for pg_config... yes
	Using config values from /usr/bin/pg_config
	checking for libpq-fe.h... no
	Can't find the 'libpq-fe.h header
	*** extconf.rb failed ***
	Could not create Makefile due to some reason, probably lack of necessary
	libraries and/or headers.  Check the mkmf.log file for more details.  You may
	need configuration options.```
30. `gem install pg -v '1.1.4' --source 'https://rubygems.org/'`
31. Error:
	```
	Building native extensions. This could take a while...
	ERROR:  Error installing pg:
        ERROR: Failed to build gem native extension.
    	current directory: /home/hey/.rvm/gems/ruby-2.6.5/gems/pg-1.1.4/ext
	/home/hey/.rvm/rubies/ruby-2.6.5/bin/ruby -I /home/hey/.rvm/rubies/ruby-2.6.5/lib/ruby/site_ruby/2.6.0 -r ./siteconf20210925-53384-9onkf4.rb 		extconf.rb
	checking for pg_config... yes
	Using config values from /usr/bin/pg_config
	checking for libpq-fe.h... no
	Can't find the 'libpq-fe.h header
	*** extconf.rb failed ***
	Could not create Makefile due to some reason, probably lack of necessary
	libraries and/or headers.  Check the mkmf.log file for more details.  You may
	need configuration options.```
32. Seems to be a postgres error log file below
	```find_executable: checking for pg_config... -------------------- yes
	--------------------
	find_header: checking for libpq-fe.h... -------------------- no
	"gcc -o conftest -I/home/hey/.rvm/rubies/ruby-2.6.5/include/ruby-2.6.0/x86_64-linux -I/home/hey/.rvm/rubies/ruby-2.6.5/include/ruby-2.6.0/ruby/backward -I/home/hey/.rvm/rubies/ruby-2.6.5/include/ruby-2.6.0 -I. -I/usr/include/postgresql    -O3 -ggdb3 -Wall -Wextra -Wdeclaration-after-statement -Wdeprecated-declarations -Wduplicated-cond -Wimplicit-function-declaration -Wimplicit-int -Wmisleading-indentation -Wpointer-arith -Wrestrict -Wwrite-strings -Wimplicit-fallthrough=0 -Wmissing-noreturn -Wno-cast-function-type -Wno-constant-logical-operand -Wno-long-long -Wno-missing-field-initializers -Wno-overlength-strings -Wno-packed-bitfield-compat -Wno-parentheses-equality -Wno-self-assign -Wno-tautological-compare -Wno-unused-parameter -Wno-unused-value -Wsuggest-attribute=format -Wsuggest-attribute=noreturn -Wunused-variable  -fPIC conftest.c  -L. -L/home/hey/.rvm/rubies/ruby-2.6.5/lib -Wl,-rpath,/home/hey/.rvm/rubies/ruby-2.6.5/lib -L/usr/lib/x86_64-linux-gnu -Wl,-rpath,/usr/lib/x86_64-linux-gnu -L. -fstack-protector-strong -rdynamic -Wl,-export-dynamic     -Wl,-rpath,'/../lib' -Wl,-rpath,'/../lib' -lruby  -lm   -lc"
	checked program was:
	/* begin */
	1: #include "ruby.h"
	2: 
	3: int main(int argc, char **argv)
	4: {
	5:   return 0;
	6: }
	/* end */
	"gcc -E -I/home/hey/.rvm/rubies/ruby-2.6.5/include/ruby-2.6.0/x86_64-linux -I/home/hey/.rvm/rubies/ruby-2.6.5/include/ruby-2.6.0/ruby/backward -I/home/hey/.rvm/rubies/ruby-2.6.5/include/ruby-2.6.0 -I. -I/usr/include/postgresql    -O3 -ggdb3 -Wall -Wextra -Wdeclaration-after-statement -Wdeprecated-declarations -Wduplicated-cond -Wimplicit-function-declaration -Wimplicit-int -Wmisleading-indentation -Wpointer-arith -Wrestrict -Wwrite-strings -Wimplicit-fallthrough=0 -Wmissing-noreturn -Wno-cast-function-type -Wno-constant-logical-operand -Wno-long-long -Wno-missing-field-initializers -Wno-overlength-strings -Wno-packed-bitfield-compat -Wno-parentheses-equality -Wno-self-assign -Wno-tautological-compare -Wno-unused-parameter -Wno-unused-value -Wsuggest-attribute=format -Wsuggest-attribute=noreturn -Wunused-variable  -fPIC  conftest.c -o conftest.i"
	conftest.c:3:10: fatal error: libpq-fe.h: No such file or directory
	    3 | #include <libpq-fe.h>
	      |          ^~~~~~~~~~~~
	compilation terminated.
	checked program was:
	/* begin */
	1: #include "ruby.h"
	2: 
	3: #include <libpq-fe.h>
	/* end */
	--------------------```
33. Maybe something with libpq-dev
34. `sudo apt install libpq-dev`
35. Error:
	`The following packages have unmet dependencies:
	 libpq-dev : Depends: libpq5 (= 12.8-0ubuntu0.20.04.1) but 13
	 E: Unable to correct problems, you have held broken packages.`
36. My libpg is too old need to revert to 12.8 `sudo sudo apt-get install libpq5=12.8-0ubuntu0.20.04.1`

**Continue from here**

37. Revert to 15 `bundle install`
38. Tells me to run `gem install pg -v '1.1.4' --source 'https://rubygems.org/` again but erro
39. Error:
	`Can't find the 'libpq-fe.h header`
40. `sudo apt-get install libpq-dev`
41. And again `em install pg -v '1.1.4' --source https://rubygems.org/` (Success)
42. Back to step 15 `bundle install` (Sucess the budle install continues now)
43. `bundle exec rake db:create db:migrate`
44. Error:
	``` 
	invalid integer value "5432 `" for connection option "port"
	't create 'libraries_development' database. Please check your configuration.
	 aborted!
	::ConnectionBad: invalid integer value "5432 `" for connection option "port"
	```
45. Seems that we have a wrong port in `database.yml` indeed a typo with ` afterwards was made
46. `bundle exec rake db:create db:migrate`
47. Error:
	```	
	(in /home/hey/git/libraries.io)
	could not connect to server: Connection refused
	Is the server running on host "localhost" (127.0.0.1) and accepting
	TCP/IP connections on port 5432?
	Couldn't create 'libraries_development' database. Please check your configuration.
	rake aborted!
	PG::ConnectionBad: could not connect to server: Connection refused
	 	Is the server running on host "localhost" (127.0.0.1) and accepting
	 	TCP/IP connections on port 5432?
	```
48. Guess postgres is not running on port 5432
49. `sudo systemctl start postgresql`
50. Should be running now `ss -tulpn | grep 5432`
	`tcp     LISTEN   0        244            127.0.0.1:5432           0.0.0.0:*`
51. Back to step 46 `bundle exec rake db:create db:migrate`
	Error:
	```
	(in /home/hey/git/libraries.io)
	FATAL:  password authentication failed for user "librariesio"
	```
52. Cannot authenticate checking database.yml again all is fine
53. Trying to go into the postgres server to create user
54. `sudo -u postgres psql`
55. `create user librariesio with password 'not-a-secret' superuser createdb;`
56. Now user postgres doesnt exist for test db
	Error:
	```
	Database 'libraries_development' already exists
	FATAL:  Peer authentication failed for user "postgres"
	Couldn't create 'libraries_test' database. Please check your configuration.
	rake aborted!
	PG::ConnectionBad: FATAL:  Peer authentication failed for user "postgres"
	```
57. Created user sameway but no sucess

**Stopping for now 3+ hours**

### Continue
58. `sudo systemctl start postgresql && systemctl start redis` still error on testdb with postgres user
59. Change `sudo vim /etc/postgresql/12/main/pg_hba.conf` to use md5 auth instead of peer
60. `bundle exec rake db:create db:migrate` Sucess seems like db got migrated
70. I go into postgres and check databases - Seems fine both test and dev is there
	```
	postgres=# \l
											List of databases
			Name          |    Owner    | Encoding |   Collate   |    Ctype    |   Access privileges
	-----------------------+-------------+----------+-------------+-------------+-----------------------
	libraries_development | librariesio | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
	libraries_test        | postgres    | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
	postgres              | postgres    | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
	template0             | postgres    | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
						|             |          |             |             | postgres=CTc/postgres
	template1             | postgres    | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
						|             |          |             |             | postgres=CTc/postgres
	(5 rows)

	```
**From this point everything should be setup correct according to the guide**

71. Go to github and create a personal access token with access to public repos
72. `bundle exec rails c` sucess got into dev env
73. Getting sample data 
```
[2] pry(main)> AuthToken.create(token: "myaccesstoken")
(0.1ms)  BEGIN /*application:Libraries*/
AuthToken Create (0.4ms)  INSERT INTO "auth_tokens" ("token", "created_at", "updated_at") VALUES ($1, $2, $3) RETURNING "id" /*application:Libraries*/  [["token", "myaccesstoken"], ["created_at", "2021-09-26 09:12:37.261718"], ["updated_at", "2021-09-26 09:12:37.261718"]]
(0.4ms)  COMMIT /*application:Libraries*/
=> #<AuthToken:0x000055c5f4b5f120 id: 1, login: nil, token: "myaccesstoken", created_at: Sun, 26 Sep 2021 09:12:37 UTC +00:00, updated_at: Sun, 26 Sep 2021 09:12:37 UTC +00:00, authorized: nil>
```
74. Continue getting sample data, though most of sample data has not be publicated and error connecting to librariesio/bibliothecary
```
PackageManager::NPM.update "pictogram"
PackageManager::Rubygems.update "split"
PackageManager::Bower.update "sbteclipse"
PackageManager::Maven.update "junit:junit"
Repository.create_from_host("github", "librariesio/bibliothecary")
```
75. Aborting sample setup, going directly to app setup
76. `bundle exec rails s` should setup rails server and point to localhost
```
hey ~/git/libraries.io (main *) $ bundle exec rails s
=> Booting Puma
=> Rails 5.2.5 application starting in development
=> Run `rails server -h` for more startup options
[18256] Puma starting in cluster mode...
[18256] * Puma version: 5.3.2 (ruby 2.6.5-p114) ("Sweetnighter")
[18256] *  Min threads: 5
[18256] *  Max threads: 5
[18256] *  Environment: development
[18256] *   Master PID: 18256
[18256] *      Workers: 2
[18256] *     Restarts: (✔) hot (✖) phased
[18256] * Preloading application
[18256] * Listening on http://127.0.0.1:3000
[18256] * Listening on http://[::1]:3000
[18256] Use Ctrl-C to stop
[18256] - Worker 0 (PID: 18266) booted in 0.0s, phase: 0
[18256] - Worker 1 (PID: 18274) booted in 0.0s, phase: 0
```
78. Going to localhost:3000
```
Faraday::ConnectionFailed in ProjectsController#index 
Couldn't connect to server
```
79. Trying their step "Github authentication and connection" but not much information is given and still connection errors

**More than 6 hours was used on this setup**