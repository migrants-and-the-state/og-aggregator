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
LOOKUP_PATH   = './build/anum_lookup.csv'
HTML_PATH     = './build/index.html'
TEMPLATE_PATH = './lib/template.html'

FETCHED_OG_INDEXES = OG_IDS.map { |id| { id => JSON.parse(HTTParty.get("https://migrants-and-the-state.github.io/#{id.downcase}/index.json").body) } }.inject(:merge)

def formatted_datetime(time = Time.now)
  fmt_date  = "#{time.month}/#{time.day}/#{time.year}"
  fmt_time  = "#{time.hour}:#{time.min.to_s.rjust(2, '0')}#{time.zone}"
  "#{fmt_date} at #{fmt_time}"
end

namespace :build do
  desc 'build aggregated list of anums and order_group ids'
  task :anums_lookup do 
    lookup = []
    FETCHED_OG_INDEXES.each { |og_id,index| index.each { |afile| lookup << [afile['id'],og_id]} }
    puts "Writing anum lookup table to #{LOOKUP_PATH}"
    File.open(LOOKUP_PATH, 'w') do |file| 
      file.puts "anumber,og_id"
      lookup.each { |pair| file.puts pair.join(",")}
    end
    puts "\nDone ✓\n"
  end 

  desc 'build aggregated index'
  task :index do    
    aggregate_index = []

    FETCHED_OG_INDEXES.each do |og_id, index|
      index.map! { |hash| hash['order_group'] = og_id; hash }
      puts "Adding #{index.length} items from #{og_id} to the aggregate index"
      index.each { |hash| aggregate_index << hash }
    end

    puts "Writing aggregated index with #{aggregate_index.length} items to #{INDEX_PATH}"
    File.open(INDEX_PATH, 'w') { |file| file.write JSON.pretty_generate(aggregate_index) }
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
    Rake::Task['build:anums_lookup'].execute 
    Rake::Task['build:index'].execute 
    Rake::Task['build:html'].execute    
  end
end

task :default => ['build:all']


