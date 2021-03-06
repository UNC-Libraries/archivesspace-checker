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
