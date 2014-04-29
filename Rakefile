require "rubygems"
require "tmpdir"

require "bundler/setup"
require "jekyll"

GITHUB_REPONAME = "apeacox/apeacox.github.io"

namespace :site do
  desc "Generate blog files"
  task :generate do
    Jekyll::Site.new(Jekyll.configuration({
      "source"      => ".",
      "destination" => "_site"
    })).process
  end


  desc "Generate and publish blog to gh-pages"
  task :publish => [:generate] do
    Dir.mktmpdir do |tmp|
      cp_r "_site/.", tmp
      Dir.chdir tmp
      system "git init"
      system "git add ."
      message = "Site updated at #{Time.now.utc}"
      system "git commit -m #{message.inspect}"
      system "git remote add origin git@github.com:#{GITHUB_REPONAME}.git"
      system "git push origin master:refs/heads/master --force"
    end
  end
end

desc 'write a blog post. (Use title="An Awesome Article")'
task :write do
  require 'fileutils'

  begin
    date = Date.parse ENV['date']
  rescue
    date = Date.today
  end

  date = date.strftime "%Y-%m-%d"
  title     = ENV['title']
  permalink = title.scan(/[[:alnum:][:space:]]/i).flatten.join.downcase.gsub(' ', '-')

  metadata = <<-YAML
---
layout: post

title: "#{ title }"
cover_image: #{ permalink }.jpg
tags:
  - programming

excerpt: ""

---


YAML

  filepath="_posts/#{date}-#{ permalink }.md"
  FileUtils.mkdir_p '_posts'
  File.open(filepath, 'w+') do |file|
    file.write metadata
  end
  exec "vim #{filepath}"

end
