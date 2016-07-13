[![Gem Version](https://badge.fury.io/rb/dupervisor.svg)](https://badge.fury.io/rb/dupervisor)
[![Build Status](https://travis-ci.org/kigster/dupervisor.svg?branch=master)](https://travis-ci.org/kigster/dupervisor)
[![Code Climate](https://codeclimate.com/github/kigster/dupervisor/badges/gpa.svg)](https://codeclimate.com/github/kigster/dupervisor)
[![Test Coverage](https://codeclimate.com/github/kigster/dupervisor/badges/coverage.svg)](https://codeclimate.com/github/kigster/dupervisor/coverage)
[![Issue Count](https://codeclimate.com/github/kigster/dupervisor/badges/issue_count.svg)](https://codeclimate.com/github/kigster/dupervisor)

# DuperVisor™ Pro 

The super-duper awesome library is your best friend if you want an easy way to convert configuration between any one of the supported formats, which at this time are: JSON, YAML, and Windows INI format. 

This tool was originally created to allow storing configs of another great tool called [__supervisord__](http://supervisord.org), which is still highly applicable today, but unfortunately uses a rather [archaic configuration file format](http://supervisord.org/configuration.html) of the decades old Windows INI format.

Whatever your preferences are, the truth is that some of the modern DevOps tools (such as Ansible and SaltStack) are using YAML format extensively to configure the environments. Ability to "embed" YAML configuration for a tool like supervisord means that you don't have to go to multiple places to see what is being run everywhere.

If you enjoy using this converted, please star the repo and we very much welcome all pull requests and contributions.

## YAML/JSON vs INI

Consider the following example taken from the [_supervisord_ configuration documentation](http://supervisord.org/configuration.html]):

```ini
[supervisord]
nodaemon = false
minfds = 1024
minprocs = 200
umask = 022
user = chrism
identifier = supervisor
directory = /tmp
nocleanup = true
childlogdir = /tmp
strip_ansi = false
environment = PATH="/usr/bin:/usr/local/bin:/bin:/sbin",ENVIRONMENT="development"

[program:cat]
command=/bin/cat
process_name=%(program_name)s
numprocs=1
directory=/tmp
umask=022
priority=999
autostart=true
autorestart=unexpected

```

We think that it is much easier to read this:

```yaml
supervisord:
  nodaemon: false
  minfds: 1024
  minprocs: 200
  umask: 022
  user: chrism
  identifier: supervisor
  directory: /tmp
  nocleanup: true
  childlogdir: /tmp
  strip_ansi: false
  environment: PATH="/usr/bin:/usr/local/bin:/bin:/sbin",ENVIRONMENT="development"

program:
  cat:
    command: /bin/cat
    process_name: %(program_name)s
    numprocs: 1
    directory: /tmp
    umask: 022
    priority: 999
    autostart: true
    autorestart: unexpected
```

Not only that, but with this structure you can leverage existing tools for merging information from the default environment to your production, and so on.

## Installation

Install the gem:

```
gem install dupervisor
``` 

or, if you are using Bundler, you can add it to your gem file like so:

```ruby
gem 'dupervisor'
```

## Usage

To perform translations in code, you would use `DuperVisor::Parser` class to parse an existing format, and `DuperVisor::Renderer` class to convert the intermediary hash into the destination format:

```ruby
      @format  = Detector.new('myfile.json').detect  # => :json

      @content = Parser.new(File.read('myfile.json')).parse()      # guesses the source format 
      @content = Parser.new(File.read('myfile.json')).parse(:json) # specify which format to parse from
      @content.format # => :json
      @content.parse_result # => { ... } Hash  

      Renderer.new(content.parse_result).render(:yaml) # => YAML string
```

You can use the provided executable `dupervisor` to convert from a JSON or YAML file into an INI

### `dv [source-file] [options]`

Summary: convert between several hierarchical configuration file formats, such as ini, yaml, json
Automatically guesses the source format based either on the file extension, or by attempting to parse it for STDIN.

#### Specific Options

```
        --ini                        Generate an INI file
        --yaml                       Generate a YAML file
        --json                       Generate a JSON file
    -o, --output [FILE]              File to write, if not supplied write to STDOUT
    -v, --verbose                    Print extra debugging info
    -h, --help                       Show this message
        --version                    Show version
```

#### Examples

__Guess input format, convert YAML format to an INI file:__

```
$ cat config.yml | dv --ini > config.ini
```

__Guess input format, convert INI format to a JSON file:__

```
$ dv config.ini --json -o config.json
```


## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/kigster/dupervisor.

## Author

<p>&copy; 2016 Konstantin Gredeskoul, all rights reserved.</p>

#### Acknowledgements

 * [Shippo, Inc.](https://goshippo.com/) for sponsoring this work financially, and their commitment to open source.
 * [Wissam Jarjoul](https://github.com/bosswissam) for many great ideas and a good eye for bugs.

## License

This project is distributed under the [MIT License](https://raw.githubusercontent.com/kigster/dupervisor/master/LICENSE).
