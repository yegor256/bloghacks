# encoding: utf-8

require 'rubygems'
require 'rake'
require 'tempfile'
require 'rake/clean'
require 'scss_lint/rake_task'

task default: [:clean, :build, :scss_lint, :pages]

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

desc "Check the existence of all critical pages"
task :pages => [:build] do
  [
    'robots.txt',
    'css/main.css',
    'CNAME',
    'about.html',
    'favicon.ico',
    'images/tomato.svg',
    'rss.xml',
    'sitemap.xml',
    '2016/09/12/first-post.html'
  ].each do |p|
    file = "_site/#{p}"
    raise "Page #{file} is not found" unless File.exists? file
  end
end
