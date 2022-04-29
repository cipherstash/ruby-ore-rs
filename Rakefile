exec(*(["bundle", "exec", $PROGRAM_NAME] + ARGV)) if ENV['BUNDLE_GEMFILE'].nil?

task :default => :test

begin
	Bundler.setup(:default, :development)
rescue Bundler::BundlerError => e
	$stderr.puts e.message
	$stderr.puts "Run `bundle install` to install missing gems"
	exit e.status_code
end

Bundler::GemHelper.install_tasks

namespace :build do
  desc "Build a 'native' package, with binary blobs included"
  task :native => ["build"] do
    sh "gem compile --include-shared-dir target pkg/#{Bundler::GemHelper.instance.gemspec.name}-#{Bundler::GemHelper.instance.gemspec.version}.gem"
  end
end

task :release do
	sh "git release"
end

require 'yard'

YARD::Rake::YardocTask.new :doc do |yardoc|
	yardoc.files = %w{lib/**/*.rb - README.md}
end

desc "Run guard"
task :guard do
	sh "guard --clear"
end

namespace :rust do
  desc "Build Rust library"
  task :build do
    sh "cargo build --release"
  end
end

require 'rspec/core/rake_task'
RSpec::Core::RakeTask.new :test => "rust:build" do |t|
	t.pattern = "spec/**/*_spec.rb"
end
