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
  filename = "./_posts/#{Time.now.strftime('%Y-%m-%d')}-#{title.to_url}.markdown"
  if File.exist?(filename)
    abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
  end
  puts "Creating new post: #{Time.now.strftime('%Y-%m-%d')}-#{title.to_url}"
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

# usage rake newpage[my-new-page] or rake newpage[my-new-page.html] or rake newpage (defaults to "new-page.markdown")
desc "Create a new page in (filename)/index.#{new_page_ext}"
task :newpage, :filename do |t, args|
  args.with_defaults(:filename => 'new-page')
  page_dir = [source_dir]
  if args.filename.downcase =~ /(^.+\/)?(.+)/
    filename, dot, extension = $2.rpartition('.').reject(&:empty?) # Get filename and extension
    title = filename
    page_dir.concat($1.downcase.sub(/^\//, '').split('/')) unless $1.nil? # Add path to page_dir Array
    if extension.nil?
      page_dir << filename
      filename = "index"
    end
    extension ||= new_page_ext
    page_dir = page_dir.map! { |d| d = d.to_url }.join('/') # Sanitize path
    filename = filename.downcase.to_url

    mkdir_p page_dir
    file = "#{page_dir}/#{filename}.#{extension}"
    if File.exist?(file)
      abort("rake aborted!") if ask("#{file} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
    end
    puts "Creating new page: #{file}"
    open(file, 'w') do |page|
      page.puts "---"
      page.puts "layout: page"
      page.puts "title: \"#{title}\""
      page.puts "date: #{Time.now.strftime('%Y-%m-%d %H:%M')}"
      page.puts "comments: true"
      page.puts "sharing: true"
      page.puts "footer: true"
      page.puts "---"
    end
  else
    puts "Syntax error: #{args.filename} contains unsupported characters"
  end
end

## Building ###
desc "Build the site"
task :build do
  Rake::Task
    puts "\n ## Scraping Google Scholar for publications"
    system "ruby _rubyscholar/scrape.rb > _includes/publications.html"
    puts "\n ##Scraping Done.\n ## committing Now."
    system "git add ."
    system "git add -u"
    puts "\n## Commiting: Site updated at #{Time.now.utc}"
    message = "Site updated at #{Time.now.utc}"
    system "git commit -m \"#{message}\""
    puts "\n ##Building now."
    system "jekyll"
end

### Previewing ###
desc "Preview the site"
task :preview do
  Rake::Task
    puts "\n ## Scraping Google Scholar for publications"
    system "ruby _rubyscholar/scrape.rb > _includes/publications.html"
    puts "\n ##Scraping Done."
    puts "\n ##Building now."
    system "jekyll serve --watch --server"
end

task :default => :start

