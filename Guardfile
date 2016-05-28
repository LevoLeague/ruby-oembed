require 'yaml'

guard 'bundler' do
  watch('Gemfile')
end

group :red_green_refactor, :halt_on_fail => true do
  guard 'rspec', :cmd => 'bundle exec rspec' do
    watch(%r{^spec/.+_spec\.rb$})
    watch(%r{^spec/cassettes/.+.yml$}) { 'spec' }
    watch(/.+\.rake$/) { 'spec' }
    watch(%r{^lib/(.+)\.rb$}) do |m|
      # Split up the file path into an Array
      path_parts = []
      remaining_path = m[1]
      while File.dirname(remaining_path) != '.'
        remaining_path, file = File.split(remaining_path)
        path_parts << file
      end
      path_parts << remaining_path
      path_parts.reverse!

      # Specs don't contain an oembed subdir
      path_parts.shift if path_parts.size > 1

      # Special case for formatter specs
      if path_parts.include?('formatter') && path_parts.include?('backends')
        path_parts.delete('backends')
        path_parts.last.gsub!(/$/, '_backend')
      end
      # Add on the _spec.rb postfix
      path_parts.last.gsub!(/$/, '_spec.rb')

      f = File.join('spec', *path_parts)
      puts "#{m.inspect} => #{f.inspect}"
      f
    end
  end

  guard :rubocop, :cmd => 'bundle exec rubocop', :keep_failed => false do
    watch(/.+\.rb$/)
    watch(/.+\.rake$/)
    watch(%r{(?:.+/)?\.rubocop\.yml$}) { |m| File.dirname(m[0]) }
  end

  # Unfortunately Code Climate's local analysis is
  # way too slow to be included in Guard :-(
  # guard :code_climate do
  #   # Watch config files
  #   watch(%r{(?:.+/)?\.(rubocop|codeclimate)\.yml$}) do |m|
  #     File.dirname(m[0])
  #   end
  #
  #   # Watch all files being rated by CodeClimate
  #   begin
  #     config_file = File.open('.codeclimate.yml', 'r')
  #     config = YAML.load(config_file.read)
  #
  #     config["ratings"]["paths"].each do |path|
  #       regexp = Regexp.escape(path)
  #       regexp.gsub!('\*\*', '.+')
  #       watch(Regexp.new(regexp))
  #     end
  #   rescue
  #     puts "Error reading/parsing .codeclimage.yml"
  #     puts $ERROR_INFO.message
  #   ensure
  #     config_file && config_file.close
  #   end
  # end
end
