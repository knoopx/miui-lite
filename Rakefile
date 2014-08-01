require 'open-uri'

REVISION = `git log -n1 --format="%h"`.chomp
FILENAME = "miui-lite-#{REVISION}.zip"
FILES = `git ls-files`.split("\n").reject { |a| a[/Rakefile/] }

task :hosts do
  File.open("system/etc/hosts", "wb") { |f| f.write(open("https://adaway.org/hosts.txt").read) }
end

task :zip do
  system "zip", FILENAME, *FILES
end

task :sideload => :default do
  system "adb", "sideload", FILENAME
end

task :default => [:hosts, :zip]
