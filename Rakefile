# -*- coding: utf-8 -*-
require 'rubygems'
require 'rake'
require 'rake/clean'
require 'rake/testtask'
require 'rake/packagetask'
require 'rake/gempackagetask'
require 'rake/rdoctask'
require 'rake/contrib/rubyforgepublisher'
require 'fileutils'
include FileUtils
require File.join(File.dirname(__FILE__), 'lib', 'image_size', 'version')

AUTHOR = 'Keisuke Minami'  # can also be an array of Authors
EMAIL = "keisuke@rccn.com"
DESCRIPTION = "measure image size(GIF, PNG, JPEG ,,, etc)"
GEM_NAME = 'imagesize' # what ppl will type to install your gem
RUBYFORGE_PROJECT = 'imagesize' # The unix name for your project
HOMEPATH = "http://#{RUBYFORGE_PROJECT}.rubyforge.org"


NAME = "imagesize"
REV = nil # UNCOMMENT IF REQUIRED: File.read(".svn/entries")[/committed-rev="(d+)"/, 1] rescue nil
VERS = ENV['VERSION'] || (Imagesize::VERSION::STRING + (REV ? ".#{REV}" : ""))
CLEAN.include ['**/.*.sw?', '*.gem', '.config', '**/.DS_Store', 'pkg/**', 'html/**' ]
RDOC_OPTS = ['--quiet', '--title', 'imagesize documentation',
    "--opname", "index.html",
    "--line-numbers", 
    "--main", "README",
    "--inline-source"]

spec = Gem::Specification.new do |p|
  p.name = NAME
  p.rubyforge_project = NAME
  p.author = AUTHOR 
  p.description = DESCRIPTION
  p.summary = DESCRIPTION
  p.email = EMAIL
  p.homepage = HOMEPATH
  p.test_files = Dir["test/**"]
  p.version = VERS
end

Rake::GemPackageTask.new(spec).define


desc 'Generate website files'
task :website_generate do
  Dir['website/**/*.txt'].each do |txt|
    sh %{ ruby scripts/txt2html #{txt} > #{txt.gsub(/txt$/,'html')} }
  end
end

desc 'Upload website files to rubyforge'
task :website_upload do
  config = YAML.load(File.read(File.expand_path("~/.rubyforge/user-config.yml")))
  host = "#{config["username"]}@rubyforge.org"
  remote_dir = "/var/www/gforge-projects/#{RUBYFORGE_PROJECT}/"
  # remote_dir = "/var/www/gforge-projects/#{RUBYFORGE_PROJECT}/#{GEM_NAME}"
  local_dir = 'website'
  sh %{rsync -av #{local_dir}/ #{host}:#{remote_dir}}
end

desc 'Generate and upload website files'
task :website => [:website_generate, :website_upload]



### ここから持ってきた設定
### http://d.hatena.ne.jp/secondlife/20061106/1162785661
Rake::RDocTask.new do |rdoc|
  rdoc.rdoc_dir = 'html'
  rdoc.options += RDOC_OPTS
  rdoc.template = "#{ENV['template']}.rb" if ENV['template']
  if ENV['DOC_FILES']
    rdoc.rdoc_files.include(ENV['DOC_FILES'].split(/,\s*/))
  else
#    rdoc.rdoc_files.include('README', 'CHANGELOG')
    rdoc.rdoc_files.include('README.txt')
#    rdoc.rdoc_files.include('lib/*.rb')
    rdoc.rdoc_files.include('lib/**/*.rb')
  end
end

desc "Publish to RubyForge"
task :rubyforge => [:rdoc, :package] do
  Rake::RubyForgePublisher.new(RUBYFORGE_PROJECT, 'gawgaw61').upload
end
