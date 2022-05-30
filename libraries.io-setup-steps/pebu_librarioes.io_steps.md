# Steps to get Libraries.io up and running

# Manual setup

> These following steps are documenting the effort of getting the Libraries.io dev enviornment up and runnning on Petya's machine.
> System Software Overview:

      System Version: macOS 11.6 (20G165)
      Kernel Version: Darwin 20.6.0
      Boot Volume: Macintosh HD

1. `git clone git@github.com:librariesio/libraries.io.git`
2. `cd ~git/libraries.io`
3. Tried to run the solution with Docker
4. ... (steps I forgot to record), but failed

### Spent 3h and stopped

### Continue

3. `brew install rbenv ruby-build`
4. `rbenv install 2.6.5`
5. `brew install --cask phantomjs homebrew/cask-versions/adoptopenjdk8`
6. `brew install redis icu4c cmake` Skipped postgres as I had it already installed
7. Created new postgres user with no password
8. Installed elasticsearch
9. `brew services start redis`
10. `brew services start postgresql`
11. `brew services start elasticsearch-full`
12. `gem install bundler && rbenv rehash`
    Failed due to rails version: ERR: Your Ruby version is 2.6.3, but your Gemfile specified 2.6.5
13. Followed Kasper's findings

    1. `rbenv init`
    1. `echo 'eval "$(rbenv init -)"' >> ~/.zshrc`
    1. `rbenv local 2.6.5`
    1. `ruby -v `
       1. `ruby 2.6.5p114 (2019-10-01 revision 67812) [x86_64-darwin20]` - success!

14. `bundle exec rake db:create db:migrate`
15. Setup a new Github access token
16. `bundle exec rails c`
17. Added access token I created: `AuthToken.create(token: "<secure github token here>")` -> success
18. Ran `PackageManager::NPM.update "pictogram"`
    failed due to View table
19. Ran this sql command: `REFRESH MATERIALIZED VIEW public.project_dependent_repositories WITH DATA;` found here: https://github.com/librariesio/libraries.io/issues/2835
20. Ran `PackageManager::NPM.update "pictogram"`
21. Failed because of elasticsearch `Elasticsearch::Transport::Transport::Errors::NotAcceptable: [406]`
22. Found out I had the latest elasticsearch, but i need a specific version
23. Stopped the elasticsearch-full service I had running
24. Downloaded https://www.elastic.co/downloads/past-releases/elasticsearch-2-4-5
25. Ran elasticsearch from the download: ` ./elasticsearch-2.4.5/bin/elasticsearch`
26. Got error:

```bash
Unrecognized VM option 'UseParNewGC'
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit.
```

27. Downgraded Java version to 1.8 due to error above: https://stackoverflow.com/a/46517346/6921525
28. Ran elasticsearch from the download: ` ./elasticsearch-2.4.5/bin/elasticsearch` -> success
29. Ran `PackageManager::NPM.update "pictogram"` -> success
30. Ran `PackageManager::Rubygems.update "split"` -> success
31. Ran `PackageManager::Bower.update` -> success
32. Ran `PackageManager::Maven.update "junit:junit"` -> success
33. Ran `Repository.create_from_host("github", "librariesio/bibliothecary")` -> success
34. Ran `rake search:reindex_everything` with result:

```bash
[!!!] Index does not exist (Elasticsearch::Transport::Transport::Errors::NotFound)
[!!!] Index does not exist (Elasticsearch::Transport::Transport::Errors::NotFound)
```

35. Ran `bundle exec rails s`
36. Can access the project at `http://localhost:3000/` with the default installed package managers: Bower, Maven, npm and Rubygems, each with 1 package
37. Ran `rake -T` to see a list of available rake tasks
38. Ran `rake download:go`, command has no output
    Nothing new in the UI after running the project again
39. Ran `rake download:go`, command has no output
    No new packages under npm package manager

## Spent 3h and stopped

**Around 6 hours were used so far**
