require 'rubygems'
require 'rake'
require 'fileutils'

new_page_ext = "markdown" # default new page file extension when using the new_page task

# usage rake newpost[my-new-post] or rake newpost['my new post'] or rake newpost (defaults to "new-post")
desc "Begin a new post in _posts"
task :newpost, :title do |t, args|
  mkdir_p "./_posts"
  args.with_defaults(:title => 'new-post')
  title = args.title
  filename = "./_posts/#{Time.now.strftime('%Y-%m-%d')}-#{title}.markdown"
  if File.exist?(filename)
    abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
  end
  puts "Creating new post: #{Time.now.strftime('%Y-%m-%d')}-#{title}"
  open(filename, 'w') do |post|
    post.puts "---"
    post.puts "layout: post"
    post.puts "title: \"#{title.gsub(/&/,'&amp;')}\""
    post.puts "date: #{Time.now.strftime('%Y-%m-%d %H:%M')}"
    post.puts "comments: true"
    post.puts "categories: "
    post.puts "---"
  end
end

### Previewing ###
desc "Scrape Google Scholar"
task :scrape do
  Rake::Task
    puts "\n ## Scraping Google Scholar for publications"
    system "ruby _rubyscholar/scrape.rb > _includes/publications.html"
    puts "\n ##Scraping Done."
end


## Building ###
desc "Build the site"
task :build do
    Rake::Task[:scrape].invoke
    puts "\n ## Committing Now."
    Rake::Task[:translit].invoke
    system "git add ."
    system "git add -u"
    puts "\n## Commiting: Site updated at #{Time.now.utc}"
    message = "Site updated at #{Time.now.utc}"
    system "git commit -m \"#{message}\""
    puts "\n ##Building now."
    system "jekyll build"
end

### Previewing ###
desc "Preview the site"
task :preview do
    system "jekyll serve --watch"
end

task :default => :start

task :translit do
  Rake::Task
    puts "\n ## Transliterating from utf-8 to ascii"
    system "find . -type f -name '*.markdown' -print -exec iconv -f utf-8 -t ascii//translit {} -o {} \\;"
    system "find . -type f -name '*.html' -print -exec iconv -f utf-8 -t ascii//translit {} -o {} \\;"
    system "find . -type f -name '*.md' -print -exec iconv -f utf-8 -t ascii//translit {} -o {} \\;"
    puts "\n ## Transliterating Done."
end
