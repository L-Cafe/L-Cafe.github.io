# == Dependencies ==============================================================

require 'rake'
require 'yaml'
require 'fileutils'
require 'rbconfig'

# == Configuration =============================================================

# Set "rake watch" as default task
task :default => :watch

# Load the configuration file
CONFIG = YAML.load_file("_config.yml")

# Get and parse the date
DATE = Time.now.strftime("%Y-%m-%d")
POST_TIME = DATE

# Directories
POSTS = "_posts"
DRAFTS = "_drafts"

# == Helpers ===================================================================

# Execute a system command
def execute(command)
  system "#{command}"
end

# Check the title
def check_title(title)
  if title.nil? or title.empty?
    raise "Please add a title to your file."
  end
end

# Transform the filename and date to a slug
def transform_to_slug(title)
  characters = /("|'|!|\?|:|\s\z)/
  whitespace = /\s/
  "#{title.gsub(characters,"").gsub(whitespace,"-").downcase}"
end

# Read the template file
def read_file(template)
  File.read(template)
end

# Save the file with the title in the YAML front matter
def write_file(content, title, directory, filename, extension)
  parsed_content = "#{content.sub("title:", "title:      \"#{title}\"")}"
  parsed_content = "#{parsed_content.sub("date:", "date:       #{POST_TIME}")}"
  parsed_content = "#{parsed_content.sub("  image:", "  image:    assets/images/#{filename}/header.jpg")}"
  parsed_content = "#{parsed_content.sub("  teaser:", "  teaser:   assets/images/#{filename}/header.jpg")}"
  File.write("#{directory}/#{filename}.#{extension}", parsed_content)
  puts "#{filename}.#{extension} was created in '#{directory}'."
end

# Create the file with the slug and open the default editor
def create_file(directory, filename, extension, content, title, editor)
  FileUtils.mkdir(directory) unless File.exists?(directory)
  if File.exists?("#{directory}/#{filename}.#{extension}")
    raise "The file already exists."
  else
    write_file(content, title, directory, filename, extension)
    if editor && !editor.nil?
      sleep 1
      execute("#{editor} #{directory}/#{filename}.#{extension}")
    end
  end
end

# Get the "open" command
def open_command
  if RbConfig::CONFIG["host_os"] =~ /mswin|mingw/
    "start"
  elsif RbConfig::CONFIG["host_os"] =~ /darwin/
    "open"
  else
    "xdg-open"
  end
end

# == Tasks =====================================================================

# rake post["Title"]
desc "Create a post in _posts"
task :post, :title do |t, args|
  title = args[:title]
  template = CONFIG["post"]["template"]
  extension = CONFIG["post"]["extension"]
  editor = CONFIG["editor"]
  check_title(title)
  filename = "#{DATE}-#{transform_to_slug(title)}"
  content = read_file(template)
  create_file(POSTS, filename, extension, content, title, editor)
end

# rake build
desc "Build the site"
task :build do
  execute("jekyll build")
end

# rake watch
desc "Serve and watch the site (with post limit or drafts)"
task :watch, :option do |t, args|
  option = args[:option]
  if option.nil? or option.empty?
    execute("jekyll serve --watch")
  end
end

# rake preview
desc "Launch a preview of the site in the browser"
task :preview do
  port = CONFIG["port"]
  if port.nil? or port.empty?
    port = 4000
  end
  Thread.new do
    puts "Launching browser for preview..."
    sleep 5
    execute("#{open_command} http://localhost:#{port}/")
  end
  Rake::Task[:watch].invoke
end
