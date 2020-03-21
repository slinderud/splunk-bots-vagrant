# Splunk Boss of the SOC v2/v3 - Vagrant lab

This project uses ansible and vagrant to start a splunk instance with Boss of
the SOC data.

The splunk-botsv2/3 role probably works on it's own.

I run this using the latest splunk version. If you find any issues please give me a headsup :)

## Requirements

- Ansible 2.9
- Vagrant 2.2.5
- vagrant-disksize
- Virtualbox

### Mac OS

```bash
brew install ansible
brew cask install virtualbox
brew cask install vagrant
vagrant plugin install vagrant-disksize

```

## Usage

Please create a `secrets.yml`. Check `secrets.yml.example` for the variables needed.

- `secrets.yml` Contains username and passwords for splunk and BOTS specific SA accounts
- `devices.yml` Defines the vagrant hosts and settings
- `deploy.yml`  Change role depending on what version of bots you want to run

```bash
vagrant up splunk
```

Then visit <http://192.168.251.10:8000/>

The CTF specific settings has to be configured manually. That is time, users/groups etc.
Please check out <https://github.com/splunk/SA-ctf_scoreboard> for the CTF configuration.

### Variables

```yaml
splunk_url_deb: URL for the deb package
splunk_base: Splunk install path
splunk_bots_version: What version of bots you want to run (botsv2 or botsv3)
splunk_botsv2_dataset: botsv2 dataset (full or attack_only)
```

### Deployment times

- 15 min - botsv2 attack-only
- 40 min - botsv2 full
- 10 min - botsv3

In the ansible playbook there is a caching mechanism so we don't actually pull
down the datasets everytime we spin up a new vm.

## Contributing

Pull requests are welcome.
I am by no means a splunk or ansible expert so fixes is appreciated.

## Thanks to

- [alias454](https://github.com/alias454/ansible-splunk-playbook) for the splunk-base role
