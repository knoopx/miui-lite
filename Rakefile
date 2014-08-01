require 'open-uri'

REVISION = `git log -n1 --format="%h"`.chomp
FILENAME = "miui-lite-#{REVISION}.zip"
FILES = Dir.glob("{META-INF,system}/**/*")

class HostParser
  REGEXPR_LINE = /^([0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3})\s+((?:(?:[a-zA-Z0-9]|[a-zA-Z0-9][a-zA-Z0-9\-]*[a-zA-Z0-9])\.)*(?:[A-Za-z0-9]|[A-Za-z0-9][A-Za-z0-9\-]*[A-Za-z0-9]))$/

  class << self
    def parse(url)
      hosts = []
      body = open(url).read
      body.each_line do |line|
        if match = line.match(REGEXPR_LINE)
          hosts << match[2] unless match[2] == "localhost"
        end
      end
      hosts
    end
  end
end

task :hosts do
  sources = [
    "https://adaway.org/hosts.txt",
    "http://winhelp2002.mvps.org/hosts.txt",
    "http://hosts-file.net/ad_servers.asp",
    "http://pgl.yoyo.org/adservers/serverlist.php?hostformat=hosts&showintro=0&mimetype=plaintext"
  ]

  hosts = sources.inject([]) { |array, url| array + HostParser.parse(url) }.sort.uniq

  File.open("system/etc/hosts", "wb") do |f|
    f.puts("127.0.0.1 localhost")
    f.puts("::1 localhost")
    f.puts
    hosts.each do |host|
      f.puts("127.0.0.1 %s" % host)
    end
  end
end

task :zip do
  system "zip", FILENAME, *FILES
end

task :sideload => :default do
  system "adb", "sideload", FILENAME
end

task :default => [:hosts, :zip]
