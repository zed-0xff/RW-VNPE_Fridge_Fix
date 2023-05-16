require 'fileutils'

task default: [:release, :clean]

desc "clean"
task :clean do
  FileUtils.rm_rf "Source/obj"
  my_name = File.basename(File.expand_path(".")) + ".dll"
  Dir["Assemblies/net472/*"].each do |fname|
    File.unlink(fname) unless File.basename(fname) == my_name
  end
  puts
  system "find . -type f -not -path './.git/*'", exception: true
  puts
  system "du -sh .", exception: true
end

desc "release"
task :release do
  Dir.chdir "Source"
  system "dotnet build -c Release", exception: true
  Dir.chdir ".."
end

namespace :readme do
  desc "render README for steam"
  task :steam do
    require 'md_to_bbcode'
  
    class SteamRenderer < MdToBbcode::BbcodeRenderer
      def header(text, header_level)
        "\n[h%d]%s[/h%d]\n" % [header_level, text, header_level]
      end

      # add Preview.png only on github
      def image(link, title, alt_text)
        if link =~ %r"^/?About/Preview\."i
          ""
        else
          super
        end
      end

      # add link to steam only on github
      def link(link, title, content)
        if content == ""
          ""
        else
          "[url=#{link}]#{content}[/url]"
        end
      end

      def paragraph(text)
        text + "\n\n"
      end
    end

    puts Redcarpet::Markdown
      .new(SteamRenderer, fenced_code_blocks: true, lax_spacing: false)
      .render(File.read("README.md")).strip.gsub("\n\n\n", "\n\n")

    git_url = "https://github.com/" + `git remote -v`.scan(/git@github\.com:(.+)\.git/).flatten.first
    puts "[url=#{git_url}]github[/url]"
  end
end
