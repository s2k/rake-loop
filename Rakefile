require 'parallel'
SPEC_DIRECTORIES = Dir['modules/*/spec']

def spec_files_in spec_dir
  Dir["#{spec_dir}/**/*_spec.rb"]
end

def has_rakefile? spec_dir
  ! Dir[ spec_dir + '/*'].select{|fn| fn.match /rakefile\Z/i }.empty?
end

def touch_failure_file(modname, report_dir)
  FileUtils.touch "#{report_dir}/#{modname}.failure.tmp"
end

def report_folder
  File.join(File.dirname(__FILE__), 'spec', 'reports')
end

namespace :spec do
  desc 'runs all rspec suites'
  task :all => :clean do
    FileUtils.mkdir_p(report_folder)

    Parallel.each(SPEC_DIRECTORIES, :in_processes => 30) do |spec_dir|
      module_name = spec_dir.split('/')[1..-2].join('-')
      puts "Processing: #{module_name}"

      # unless has_rakefile? spec_dir + '/../'
      #   warn "WARN: No Rakefile found for #{File.expand_path spec_dir} - skipping #{spec_dir}"
      #   next
      # end

      rake_exit_status = :unset
      begin
        sh "cd #{spec_dir}/../; bundle exec rake spec > #{report_folder}/#{module_name}.tmp 2>&1"
        rake_exit_status = $?.exitstatus
      rescue
        touch_failure_file(module_name, report_folder)
      end

      puts "rake exit status: #{rake_exit_status} (#{rake_exit_status.class}) for #{spec_dir}"

      if rake_exit_status != 0
        touch_failure_file(module_name, report_folder)
      end
    end

    sh "cat #{report_folder}/*.tmp"

    failed_module_files = Dir["#{report_folder}/*.failure.tmp"]
    failed_module_names = failed_module_files.map { |f| File.basename(f).gsub('.failure.tmp', '') }

    unless failed_module_names.empty?
      fail "Task :run_specs failed for the following modules: #{failed_module_names.join(', ')}"
    end
  end


  desc 'delete old report files'
  task :clean do
    FileUtils.rm_rf report_folder
  end
end

task :spec do
  sh 'bundle exec rake spec:all'
end