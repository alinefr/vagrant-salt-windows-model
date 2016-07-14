# -*- mode: ruby -*-
# vi: set ft=ruby :
$script = <<SCRIPT
$git_version = '2.9.0'
$zipfile = "MinGit-$git_version-preview-64-bit.zip"
$url = "https://github.com/git-for-windows/git/releases/download/v$git_version.windows.1/$zipfile"
$dest_dir = "c:\\Users\\vagrant\\Downloads"
$git_exec = "c:\\Program \Files\\Git\\bin\\git.exe"

# Download, Unzip and install git
if (-Not (Get-Command "git" -ErrorAction SilentlyContinue)) {
  if (-Not (Test-Path -Path $dest_dir\\$zipfile)) {
    try {
      Invoke-WebRequest -OutFile $dest_dir\\$zipfile $url -ErrorAction Stop
    } Catch {
      $ErrorMessage = $_.Exception.Message
      "Failed to download $zipfile."
      "ERROR: $ErrorMessage"
    }
  }
  try {
    Add-Type -AssemblyName System.IO.Compression.FileSystem
    [System.IO.Compression.ZipFile]::ExtractToDirectory("$dest_dir\\$zipfile", 'c:\\Program \Files\\Git')
  } Catch {
    $ErrorMessage = $_.Exception.Message
    "Failed to unzip $zipfile."
    "ERROR: $ErrorMessage"
  }
  $PATH = $env:Path + ";c:\\Program\ Files\\Git\\cmd"
  [Environment]::SetEnvironmentVariable("Path", "$PATH", [EnvironmentVariableTarget]::Machine)
} 

# Add salt to system Path. Checks whether $PATH is already set (git).
if (-Not (Get-Command "salt-call" -ErrorAction SilentlyContinue)) {
  if (Get-Variable PATH -Scope Global -ErrorAction SilentlyContinue) {
    $PATH = $PATH + ";c:\\salt"
  } else {
    $PATH = $env:Path + ";c:\\salt"
  }
  [Environment]::SetEnvironmentVariable("Path", "$PATH",  [EnvironmentVariableTarget]::Machine)
}
SCRIPT

Vagrant.configure(2) do |config|
  config.vm.box = "opentable/win-2012r2-standard-amd64-nocm"
  config.vm.synced_folder "provision/salt", "/salt/file_roots/salt"
  config.vm.provider "virtualbox" do |v|
    v.gui = true
  end
  config.vm.provision "shell", inline: $script 
  config.vm.provision :salt do |salt|
    salt.bootstrap_options = "-version 2016.3.1"
    salt.masterless = true
    salt.minion_config = "provision/minion"
    salt.colorize = true
    salt.verbose = true
    salt.log_level = "warning" 
  end
end
