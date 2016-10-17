require 'guard/compat/plugin'

module ::Guard
  class RubyHtmlSyntaxFixer < Plugin

    def run_on_modifications(paths)
      Guard.pause(:paused)
      paths.each do |path|
        matches_to_replace = []
        found_tags = []
        text = File.read(path)

        # Find ruby tags and store the match's string value
        text.scan(/(<%.*%>)/) do |match|
          found_tags << match[0].to_s
        end

        # Look through all ruby tags
        found_tags.each do |tag|
          # Find all double quote strings
          tag.scan(/(".+?")/) do |group|
            match = group[0].to_s
            # Skip if it includes string interpolation
            next if match.include?('#{')

            # Replace double quote with single quote
            fixed_group = match.tr('"', "'")
            fixed_tag = tag.gsub(match, fixed_group)

            # Store the original tag with the fixed one
            matches_to_replace << [tag, fixed_tag]
          end
        end

        # Replace the file content with each fixed tag
        matches_to_replace.each do |matches|
          text.gsub!(matches[0], matches[1])
        end

        # Save file with new contents
        File.open(path, 'w') { |file| file.puts text }
      end
      Guard.pause(:unpaused)
    end

  end
end

guard 'ruby_html_syntax_fixer' do
  watch(%r{app/views/.+\.(erb|haml|slim)})
end
