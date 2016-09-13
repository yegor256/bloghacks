# encoding: utf-8
require 'rubygems'
require 'rake'
require 'tempfile'
require 'rake/clean'
require 'scss_lint/rake_task'
task default: [:clean, :build, :scss_lint]

desc "Lint SASS sources"
SCSSLint::RakeTask.new do |t|
  f = Tempfile.new(['bloghacks-', '.scss'])
  f.write File.open('css/main.scss').drop(2).join("\n")
  f.close
  t.files = Dir.glob([f.path])
end

desc "Build Jekyll site"
task :build do
  system("jekyll build")
end
