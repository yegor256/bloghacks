# encoding: utf-8
require 'rubygems'
require 'rake'
require 'rake/clean'
require 'scss_lint/rake_task'
task default: [:clean, :build, :scss_lint]

desc "Lint SASS sources"
SCSSLint::RakeTask.new do |t|
  t.name = 'scss_lint'
  t.files = ['css']
end

desc "Build Jekyll site"
task :build do
  system("jekyll build")
end
