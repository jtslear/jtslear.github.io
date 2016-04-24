# coding: utf-8
require 'colorize'
require 'html-proofer'
require 'jekyll'
require 'rubocop/rake_task'
require 'uri'

# Configuration Options
config_file = '_config.yml' # Name of Jekyll config file

# Do not touch below this line
RuboCop::RakeTask.new

# Extend string to allow for bold text.
class String
  def bold
    "\033[1m#{self}\033[0m"
  end
end

# Rake Jekyll tasks
task :build do
  puts 'Cleaning local site...'.yellow.bold
  Rake::Task['clean'].invoke
  puts 'Building site...'.yellow.bold
  Jekyll::Commands::Build.process(profile: true)
end

task :serve do
  Rake::Task['clean'].invoke
  puts 'Building site...'.yellow.bold
  sh "cp _config.yml _config_dev.yml"
  sh "sed -i -dev -e 's/^url:/#url:/' _config_dev.yml"
  sh "jekyll build --config _config_dev.yml"
  puts 'Begining to serve site...'.yellow.bold
  begin
    sh "jekyll serve --config _config_dev.yml"
  rescue SystemExit, Interrupt
    sh "rm -rf _config_dev.yml"
    sh "rm -rf _config_dev.yml-dev"
    exit 0
  rescue Exception => e
    puts e
  end
end

task :clean do
  puts 'Cleaning up _site...'.yellow.bold
  Jekyll::Commands::Clean.process({})
end

task :html_proofer do
  Rake::Task['build'].invoke
  host_regex = Regexp.new(site_domain(config_file))
  puts 'Running html proofer...'.yellow.bold
  HTMLProofer.check_directory(
    './_site',
    allow_hash_href: true,
    url_ignore: [host_regex],
    file_ignore: ['^vendor/.*']
  ).run
end

# Misc Methods
def site_domain(config_file)
  URI(fetch_jekyll_config(config_file)['url']).host
end

def fetch_jekyll_config(config_file)
  site = Jekyll::Configuration.new
  site.read_config_file(config_file)
end
