require "pathname"

APP_ROOT = Pathname.new(File.expand_path(".", __FILE__))

desc "create a new recipe"
task :new do |t, args|
  raise "need a TITLE" unless ENV["TITLE"]

  post_title = ENV["TITLE"]
  base_filename = post_title.downcase.gsub(/\s+/, "-")
  recipe_path = APP_ROOT.join("../_recipes", base_filename + ".md")

  raise "recipe already exists" if File.exist?(recipe_path)

  print "creating #{recipe_path}..."
  File.open(recipe_path, "w") do |f|
    f.write(<<~EOF)
      ---

      layout: recipe
      title: "#{post_title}"
      tags: tags, go, here

      ingredients:

      directions:

      ---

      add notes
    EOF
  end

  print " done!"
end
