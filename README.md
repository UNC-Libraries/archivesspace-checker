# Archivesspace Checker

This repository has been forked from Harvard Library's [ArchivesSpace Checker](https://github.com/harvard-library/archivesspace-checker). 
Major changes include creating a command line analysis tool and customizing the schematron for UNC EADs.

This is a small website intended to allow archivists to check their EAD files prior to ingest by Archivesspace. 
EADs can also be analyzed in bulk via command line rake task.

## System Requirements

* JRuby 9.2.11+
* JDK 13
* Bundler 2.1.4

## Installation Instructions

```sh
git clone git@github.com:UNC-Libraries/archivesspace-checker.git
cd archivesspace-checker
```

Install jruby using rvm:
```sh
rvm list known
rvm install jruby-9.2.12.0
rvm --ruby-version use jruby-9.2.12.0
```

Install jruby using rbenv and restrict jruby version to current directory:
```shell
rbenv install -l
rbenv install jruby-9.2.12.0
rbenv local jruby-9.2.12.0
```

Note: You may need to install bundler after setting the jruby version
```shell
gem install bundler
```

Set JAVA_HOME:

Get java.home from: ```java -XshowSettings:properties -version```
```sh
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk-13.jdk/Contents/Home
```

Install gems:
```sh
bundle
bundle exec rackup
```

Then direct your browser to localhost:9292, upload some EADs, and enjoy!

If you get errors on start up make sure there's no WEB-INF directory or archivesspacecheker.rb file in tmp. 
If so delete them. These are build files from the war that rack doesn't know what to do with.

## Building WAR file for production
This app wasn't originally meant to be a generated war file


So the files need to be rejiggered a bit

1. run 
```shell
bundle
```
to make sure the warbler gem is installed

1. In Gemfile comment out the following gems (They don't do any harm, but bloat the war file)
   * gem 'therubyrhino'
   * gem 'yard'
   * gem 'yard-sinatra'
   * gem 'puma'
   * gem 'rake'
   * gem 'warbler'
   
2. In config/evironment comment out
   ```ruby
    if ENV['RACK_ENV'] == 'development'
    Bundler.require(:development)
    end
    Dir['./**/*.rb'].reject {|s| s.match(/\A\.\/(test|config\/deploy)/)}.map { |s| require s.sub(/\.rb\z/, '')}
    ```
   
3. In RakeFile comment out the "test" task
```ruby
Bundler.require(:test)

# Need to comment out the rake/testtask block to build war file
require 'rake/testtask'

Rake::TestTask.new do |t|
  t.test_files = FileList['test/test*.rb']
  t.verbose = true
  t.warning = false
end

task :default => :test
```

4. run 
```sh
warble compiled war
```

## Configuration

Configuration settings can be included by putting a YAML file at `config/config.yml`

Right now, the only setting checked for is `schematron`, which is the location that
the schematron file being used is located at.

## Command Line Usage Instructions

Multiple EADs can be analyzed in bulk via command line, with errors output to CSV format in `tmp/result_TIMESTAMP.csv`:

    rake analyze_eads EADS=/path/to/EADs FILE=/path/to/schematron

## Tests

Run automated tests:

```sh
bundle exec rake test
```

### Large Finding Aids

You may find that the app fails to work over especially large finding aids with the default JVM memory settings.
It's possible to increase the amount of heap memory available to the JVM (and tune other JVM settings) by passing options
via the environment variable `JRUBY_OPTS`.  Options for the JVM are prefixed by `-J`; for example, to set the maximum memory size to 1gb:

``` shell
JRUBY_OPTS=-J-Xmx1G
```

## Schematron notes

When writing Schematron, a common source of errors is assuming that Schematron understands default xmlns namespaces.  It very much does not.  If you set something up as a default namespace, and reference elements without a prefix in Schematron tests, they will be ignored.  Always either provide an explicit prefix, or else use the wildcard prefix (e.g. `/ead:ead` or `/*:ead` instead of `/ead`).

## Developer Documentation
Documentation generated via YARD is available [here](http://harvard-library.github.io/archivesspace-checker).

## Contributors
* [Dave Mayo](https://github.com/pobocks)
* [Christina Cortland](https://github.com/chrisrlc)

## License
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this software except in compliance with the License.
You may obtain a copy of the License at

[http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License."

## Copyright
© 2014 President and Fellows of Harvard College
