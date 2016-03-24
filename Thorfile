# -*- mode: ruby; coding: utf-8; -*-

require 'rubocop'
# 'travis lint' can be called as a subprocess, but calling it from Ruby is
# preferable because it is cleaner and avoids loading the entire travis gem.
require 'travis/cli/lint'
require 'mixlib/shellout'
require 'artii'
require 'colorize'
require 'shellwords'

# Helper module for safely executing subprocesses.
module Subprocess
  # Mixlib::ShellOut doesn't support arrays on Windows... Ugh.
  def run_subprocess(cmd)
    # See `bundle help exec' for more info on using a 'clean' environment.
    Bundler.with_clean_env do
      puts "> #{cmd.shelljoin}".colorize(mode: :bold)
      proc = Mixlib::ShellOut.new(cmd, live_stream: STDOUT)
      proc.run_command
      proc
    end
  end
end

# Tasks for testing.
class Test < Thor
  include Subprocess

  FORMULA_PATH = 'Formula/dmgbuild.rb'.freeze

  desc 'rubocop', 'Run rubocop on all Ruby files'
  def rubocop(exit = true)
    # Don't check the formula; this is done by Homebrew in the audit.
    result = RuboCop::CLI.new.run %W(Gemfile #{__FILE__})
    puts 'No rubocop errors'.colorize(:green) if result == 0
    exit result if exit
    result
  end

  desc 'audit', "Run 'brew audit' on the formula"
  def audit(exit = true)
    proc = run_subprocess %W(brew audit --strict --online #{FORMULA_PATH})
    exit proc.exitstatus if exit
    proc.exitstatus
  end

  desc 'install', 'Install dmgbuild, then test the formula'
  def install(exit = true)
    install_args = %w(brew install --verbose)
    install_args.push '--HEAD' if ENV['DMGBUILD_VERSION'] == 'HEAD'
    install_args.push FORMULA_PATH
    proc = run_subprocess install_args
    proc.error!
    proc = run_subprocess %W(brew test --no-sandbox --verbose #{FORMULA_PATH})
    exit proc.exitstatus if exit
    proc.exitstatus
  end

  desc 'travis', "Run 'travis lint' on '.travis.yml'"
  def travis(exit = true)
    puts 'Linting travis file'
    lint = Travis::CLI::Lint.new
    lint.exit_code = true # Make lint exit with an exit code
    begin
      lint.run
    rescue SystemExit => e
      retval = e.status
    else
      # Travis::CLI::Lint.run will only exit if the file has errors
      retval = 0
    end
    exit retval if exit
    retval
  end

  desc 'ci', 'Run tests for continuous integration'
  def ci
    # TODO: Add travis back in here once 'travis lint' recognizes the
    # 'osx_image' key.
    #
    # Note: audit is already run by 'brew test-bot', so it's not necessary to
    # run it here.
    run_tests %w(rubocop install)
  end

  desc 'all', 'Run all tests'
  def all
    run_tests %w(rubocop audit install travis)
  end

  private

  def run_tests(tasks)
    # Pass false as an argument to prevent the task from exiting.
    sum = tasks.collect { |task| invoke('test:' + task, [false]) }.reduce(:+)
    if sum == 0
      print_msg 'PASS', :green
    else
      print_msg 'FAIL', :red
    end
    # Exit with the sum of the error codes.
    exit sum
  end

  def print_msg(msg, color)
    artii = Artii::Base.new font: 'block'
    # artii adds two blank lines after the block text; we just want one.
    print artii.asciify(msg).lines[0..-2].join.colorize(color)
  end
end
