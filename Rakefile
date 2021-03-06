require 'rake'
require 'rake/testtask'
require 'rake/rdoctask'
require 'rake/packagetask'
require 'rake/gempackagetask'
require 'rake/contrib/rubyforgepublisher'

require "./lib/capistrano/version"

PKG_NAME      = "capistrano"
PKG_BUILD     = ENV['PKG_BUILD'] ? '.' + ENV['PKG_BUILD'] : ''
PKG_VERSION   = Capistrano::Version::STRING + PKG_BUILD
PKG_FILE_NAME = "#{PKG_NAME}-#{PKG_VERSION}"

desc "Default task"
task :default => [ :test ]

desc "Build documentation"
task :doc => [ :rdoc ]

Rake::TestTask.new do |t|
  t.libs << "test"
  t.test_files = Dir["test/**/*_test.rb"]
  t.verbose = true
end

desc "Run code-coverage analysis using rcov"
task :coverage do
  rm_rf "coverage"
  files = Dir["test/**/*_test.rb"]
  system "rcov --sort coverage -Ilib:test #{files.join(' ')}"
end

GEM_SPEC = eval(File.read("#{File.dirname(__FILE__)}/#{PKG_NAME}.gemspec"))

Rake::GemPackageTask.new(GEM_SPEC) do |p|
  p.gem_spec = GEM_SPEC
  p.need_tar = true
  p.need_zip = true
end

desc "Build the RDoc API documentation"
Rake::RDocTask.new do |rdoc|
  rdoc.rdoc_dir = "doc"
  rdoc.title    = "Capistrano -- A framework for remote command execution"
  rdoc.options += %w(--line-numbers --inline-source --main README)
  rdoc.rdoc_files.include 'README'
  rdoc.rdoc_files.include 'lib/**/*.rb'
  rdoc.template = "jamis"
end

desc "Publish the beta gem"
task :pgem => [:package] do 
  Rake::SshFilePublisher.new("wrath.rubyonrails.org", "public_html/gems/gems", "pkg", "#{PKG_FILE_NAME}.gem").upload
  `ssh wrath.rubyonrails.org './gemupdate.sh'`
end

desc "Clean up generated directories and files"
task :clean do
  rm_rf "pkg"
  rm_rf "doc"
  rm_rf "coverage"
end
