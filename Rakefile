# encoding: utf-8

require 'rubygems'
require 'rake'
require 'tempfile'
require 'rake/clean'
require 'scss_lint/rake_task'
require 'w3c_validators'
require 'nokogiri'
require 'rubocop/rake_task'
require 'English'
require 'net/http'
require 'html-proofer'

task default: [
  :clean,
  :build,
  :scss_lint,
  :pages,
  :garbage,
  :proofer,
  :spell,
  :ping,
  :orphans,
  :rubocop
]

def done(msg)
  puts msg + "\n\n"
end

desc 'Delete _site directory'
task :clean do
  rm_rf '_site'
  done 'Jekyll site directory deleted'
end

desc 'Lint SASS sources'
SCSSLint::RakeTask.new do |t|
  f = Tempfile.new(['bloghacks-', '.scss'])
  f << File.open('css/main.scss').drop(2).join("\n")
  f.flush
  f.close
  t.files = Dir.glob([f.path])
end

desc 'Build Jekyll site'
task :build do
  if File.exist? '_site'
    done 'Jekyll site already exists in _site'
  else
    system('jekyll build')
    fail 'Jekyll failed' unless $CHILD_STATUS.success?
    done 'Jekyll site generated without issues'
  end
end

desc 'Check the existence of all critical pages'
task pages: [:build] do
  File.open('_rake/pages.txt').map(&:strip).each do |p|
    file = "_site/#{p}"
    fail "Page #{file} is not found" unless File.exist? file
    puts "#{file}: OK"
  end
  done 'All files are in place'
end

desc 'Check the absence of garbage'
task garbage: [:build] do
  File.open('_rake/garbage.txt').map(&:strip).each do |p|
    file = "_site/#{p}"
    fail "Page #{file} is still there" if File.exist? file
    puts "#{file}: absent, OK"
  end
  done 'There is no garbage'
end

desc 'Validate a few pages for W3C compliance'
# It doesn't work now, because of: https://github.com/alexdunae/w3c_validators/issues/16
task w3c: [:build] do
  include W3CValidators
  validator = MarkupValidator.new
  [
    'index.html',
    '2016/09/12/first-post.html'
  ].each do |p|
    file = "_site/#{p}"
    results = validator.validate_file(file)
    if results.errors.length > 0
      results.errors.each do |err|
        puts err.to_s
      end
      fail "Page #{file} is not W3C compliant"
    end
    puts "#{p}: OK"
  end
  done 'HTML is W3C compliant'
end

desc 'Validate a few pages through HTML proofer'
task proofer: [:build] do
  HTMLProofer.check_directory(
    '_site',
    log_level: :warn,
    check_favicon: true,
    check_html: true
  ).run
  done 'HTML passed through html-proofer'
end

desc 'Check spelling in all HTML pages'
task spell: [:build] do
  Dir['_site/**/*.html'].each do |f|
    html = Nokogiri::HTML(File.read(f))
    html.search('//code').remove
    html.search('//script').remove
    html.search('//pre').remove
    html.search('//header').remove
    html.search('//footer').remove
    text = html.xpath('/html/body/section//p').text
    tmp = Tempfile.new(['bloghacks-', '.txt'])
    tmp << text
    tmp.flush
    tmp.close
    stdout = `cat "#{tmp.path}" \
      | aspell -a --lang=en_US -W 2 --ignore-case -p ./_rake/aspell.en.pws \
      | grep ^\\&`
    fail "Typos at #{f}:\n#{stdout}" unless stdout.empty?
    puts "#{f}: OK (#{text.split(/\s/).size} words)"
  end
  done 'No spelling errors'
end

desc 'Ping all foreign links'
task ping: [:build] do
  links = Dir['_site/**/*.html'].reduce([]) do |array, f|
    array + Nokogiri::HTML(File.read(f)).xpath(
      '//a/@href[starts-with(.,"http://") or starts-with(.,"https://")]'
    ).to_a.map(&:to_s)
  end.uniq
  links.map { |u| URI.parse(u) }.each do |uri|
    http = Net::HTTP.new(uri.host, uri.port)
    http.use_ssl = true if uri.port == 443
    data = http.head(uri.request_uri)
    puts "#{uri}: #{data.code}"
    fail "URI #{uri} is not OK" unless data.code == '200'
  end
  done 'All links are valid'
end

desc 'Make sure there are no orphan articles'
task orphans: [:build] do
  links = Dir['_site/**/*.html'].reduce([]) do |array, f|
    array + Nokogiri::HTML(File.read(f)).xpath('//a/@href').to_a.map(&:to_s)
  end
  links = links
    .map { |a| a.gsub(/^\//, 'http://bloghacks.yegor256.com/') }
    .reject { |a| !a.start_with? 'http://bloghacks.yegor256.com/' }
    .map { |a| a.gsub(/#.*/, '') }
  links += Dir['_site/**/*.html']
    .map { |f| f.gsub(/_site/, 'http://bloghacks.yegor256.com') }
  counts = {}
  links
    .reject { |a| !a.match %r{.*/[0-9]{4}/[0-9]{2}/[0-9]{2}/.*} }
    .group_by(&:itself).each { |k, v| counts[k] = v.length }
  orphans = 0
  counts.each do |k, v|
    if v < 3
      puts "#{k} is an orphan (#{v})"
      orphans += 1
    else
      puts "#{k}: #{v}"
    end
  end
  fail "There are #{orphans} orphans" unless orphans == 0
  done "There are no orphans in #{links.size} links"
end

desc 'Run RuboCop on all Ruby files'
RuboCop::RakeTask.new do |t|
  t.fail_on_error = true
  t.requires << 'rubocop-rspec'
end
