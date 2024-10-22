require 'httparty'
require 'json'
require 'liquid'

OG_IDS = [
  'OG-2024-USCIS',
  'OG-2024-SF-NARA',
  'OG-2024-KC-NARA',
  'OG-2023-KC-NARA',
  'OG-2022-KC-NARA',
  'OG-2021-USCIS'
]
INDEX_PATH    = './build/index.json'
HTML_PATH     = './build/index.html'
TEMPLATE_PATH = './lib/template.html'

def formatted_datetime(time = Time.now)
  fmt_date  = "#{time.month}/#{time.day}/#{time.year}"
  fmt_time  = "#{time.hour}:#{time.min.to_s.rjust(2, '0')}#{time.zone}"
  "#{fmt_date} at #{fmt_time}"
end

namespace :build do
  desc 'build aggregated index'
  task :index do    
    index = []

    OG_IDS.each do |og_id|
      url     = "https://migrants-and-the-state.github.io/#{og_id.downcase}/index.json"
      hashes  = JSON.parse(HTTParty.get(url).body)
      hashes.map! { |h| h['order_group'] = og_id; h }
      puts "Adding #{hashes.length} items from #{og_id} to the index"
      hashes.each { |hash| index << hash }
    end

    puts "Writing aggregated index with #{index.length} items to #{INDEX_PATH}"
    File.open(INDEX_PATH, 'w') { |file| file.write JSON.pretty_generate(index) }
    puts "\nDone ✓\n"
  end

  desc 'build html'
  task :html do 
    vars  = {
      'items' => JSON.load(File.open(INDEX_PATH)),
      'formatted_date' => formatted_datetime
    }
    template = Liquid::Template.parse File.open(TEMPLATE_PATH).read

    puts "Writing html dashboard to #{HTML_PATH}"
    File.open(HTML_PATH, 'w') { |file| file.write template.render(vars) }
    puts "\nDone ✓\n"
  end

  desc 'build everything'
  task :all do 
    Rake::Task['build:index'].execute 
    Rake::Task['build:html'].execute    
  end
end

task :default => ['build:all']


