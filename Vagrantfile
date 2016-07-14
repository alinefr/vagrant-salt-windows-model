# -*- mode: ruby -*-
# vi: set ft=ruby :
$script = <<SCRIPT
$url = "https://github.com/git-for-windows/git/releases/download/v2.9.0.windows.1/MinGit-2.9.0-preview-64-bit.zip"
$zipfile = "MinGit-2.9.0-preview-64-bit.zip"
$dest_dir = "c:\\Users\\vagrant\\Downloads"
$git_exec = "c:\\Program \Files\\Git\\bin\\git.exe"

# Download, Unzip and install git
if (-Not (Test-Path -Path $git_exec)) {
  if (-Not (Test-Path -Path $dest_dir\\$zipfile)) {
    try {
      Invoke-WebRequest -OutFile $dest_dir\\$zipfile $url -ErrorAction Stop
    } Catch {
      $ErrorMessage = $_.Exception.Message
      $FailedItem = $_.Exception.ItemName
      "Failed to download $zipfile in $FailedItem."
      "ERROR: $ErrorMessage"
    }
  }
  try {
    Add-Type -AssemblyName System.IO.Compression.FileSystem
    [System.IO.Compression.ZipFile]::ExtractToDirectory("$dest_dir\\$zipfile", 'c:\\Program \Files\\Git')
  } Catch {
    $ErrorMessage = $_.Exception.Message
    $FailedItem = $_.Exception.ItemName
    "Failed to unzip $zipfile in $FailedItem."
    "ERROR: $ErrorMessage"
  }
  [Environment]::SetEnvironmentVariable("Path", $env:Path + ";C:\\Program \Files\\Git\\cmd", [EnvironmentVariableTarget]::Machine)
} 

# Add salt to system Path
if (-Not (Get-Command "salt-call" -ErrorAction SilentlyContinue)) {
  [Environment]::SetEnvironmentVariable("Path", $env:Path + ";C:\\salt", [EnvironmentVariableTarget]::Machine)
}
SCRIPT

Vagrant.configure(2) do |config|
  config.vm.box = "opentable/win-2012r2-standard-amd64-nocm"
  config.vm.synced_folder "provision/salt", "/salt/file_roots/salt"
  config.vm.provider "virtualbox" do |v|
    v.gui = false
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
