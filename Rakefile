require './constants'
require 'json'
require 'set'

task :features do
  sh "tippecanoe-json-tool #{SRC_PATH}"
end

task :index do
  sh "rake features | rake _index"
end

task :_index do
  n03_007 = Set.new
  while STDIN.gets
    f = JSON.parse($_)
    n03_007 << f['properties']['N03_007'] if f['properties']['N03_007']
  end
  json = {
    :n03_007 => n03_007.sort
  }
  File.open('index.json', 'w') {|w|
    w.print JSON.pretty_generate(json)
  }
end 

task :generate do
  n03_007 = JSON.parse(File.open('index.json').read)
  n03_007['n03_007'].each {|city|
    dst_path = "#{city}.json"
    print "creating #{dst_path}\n" 
    feat = {
      :type => 'Feature',
      :properties => {
        :N03_007 => city
      },
      :geometry => {
        :type => 'MultiPolygon',
        :coordinates => []
      }
    }
    IO.popen('rake features', 'r') {|i|
      while i.gets
        f = JSON.parse($_)
        if f['properties']['N03_007'] == city
          if feat[:properties][:N03_001].nil?
            feat[:properties][:N03_001] = f['properties']['N03_001']
            feat[:properties][:N03_002] = f['properties']['N03_002']
            feat[:properties][:N03_003] = f['properties']['N03_003']
            feat[:properties][:N03_004] = f['properties']['N03_004']
          end
          feat[:geometry][:coordinates] << f['geometry']['coordinates']
        end
      end 
    }
    File.open("#{city}.geojson", 'w') {|w|
      w.print JSON.dump(feat)
    }
  }
end

