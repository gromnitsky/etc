require "bundler/gem_tasks"
require "rake/testtask"

name = "etc"
specfile = name.tr("/", "-")+".gemspec"

VERSIONS = %w[2.2.2 2.3.0 2.4.0 2.5.0]

headers = ["ext/etc/constdefs.h"]
task compile: headers
task build: headers
file "ext/etc/constdefs.h" => "ext/etc/mkconstants.rb" do |t|
  ruby t.prerequisites.first, "-o", t.name
end

Rake::TestTask.new(:test) do |t|
  t.libs << "test" << "test/lib"
  t.libs << "lib"
  t.test_files = FileList["test/**/test_*.rb"]
end

require 'rake/extensiontask'
spec = eval(File.read(specfile), nil, specfile)
spec.files.delete_if {|n| %r'\Aext/' =~ n}
spec.extensions.clear
spec.require_paths.insert(0, *%w[stub])
Rake::ExtensionTask.new(name, spec) do |ext|
  ext.cross_compile = true
  ext.cross_platform = %w[x86-mingw32 x64-mingw32]
  ext.cross_compiling do |s|
    s.files.concat VERSIONS.map {|v| "lib/#{v[/\A\d+\.\d+/]}/#{name}.so"}
  end
end

desc "Compile binaries for mingw platform using rake-compiler-dock"
task 'build:mingw' do
  require 'rake_compiler_dock'
  RakeCompilerDock.sh "bundle && rake cross native gem RUBY_CC_VERSION=#{VERSIONS.join(':')}"
end
task 'build:mingw' => headers

task :default => [:compile, :test]
