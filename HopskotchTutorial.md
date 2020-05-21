# Hopskotch Tutorial Script

## Resources

### Source code
* `hop-client`: https://github.com/scimma/hop-client
  * Docs: https://hop-client.readthedocs.io/en/latest/index.html
* hop app template: https://github.com/scimma/hop-app-template

### Package repositories
* conda: https://anaconda.org/scimma/hop-client
* pypi: https://pypi.org/project/hop-client/


## Session 1: Installing and using hop-client

Reminder: you must have Python version >= 3.6 installed. If using `conda`, python 3.7 is required at the moment.

To install `hop-client`, you must have either `pip` or `conda`. This tutorial also uses `curl`. If you're not using `conda`, here are some hints on how to install these on some common Linux systems:
* Debian (including Ubuntu):
  ```
   sudo apt-get update && sudo apt-get install python3-pip curl
   ```
* Centos:
  ```
  yum install python36 python36-devel python36-setuptools curl
  easy_install-3.6 pip
  ```
### Getting set up
Make a new directory for this demo and move to it:
```
mkdir scimma_demo/ && cd scimma_demo
```

### Setting up a virtual environment
Running this demo in a virtual environment will help keep your default system packages and the demo's installation dependencies separate. You can use either pip or conda to create a virtual environment:

* pip:
  ```
  python3 -m venv demo-venv
  source demo-venv/bin/activate
  ```
* conda:
  ```
  conda create --name demo-venv python=3.7
  conda activate demo-venv
  ```

### Installing Hop-Client
`hop-client` can be installed with either `pip` or `conda`:
* pip
  ```
   pip install -U hop-client
  ```
* conda
  ```
  conda install --channel conda-forge --channel scimma hop-client
  ```

Verify your install by checking `hop`'s help:
```
hop --help
```

You should see options for publishing and subscribing.

### Getting files
You will need an authentication configuration file to send/receive messages with hop-client. You can download it either by using `curl`:
```
curl  https://raw.githubusercontent.com/scimma/may2020-techthon-demo/master/config.conf -o config.conf
```
OR go to https://raw.githubusercontent.com/scimma/may2020-techthon-demo/master/config.conf  and right-click - select "Save Page as " - save as config.conf in your current directory.

You will also need a sample file containing a message we will send and receive. Download it with either:
```
curl https://raw.githubusercontent.com/scimma/may2020-techthon-demo/master/example.gcn3 -o example.gcn3
```
OR go to https://raw.githubusercontent.com/scimma/may2020-techthon-demo/master/example.gcn3 and right-click - select "Save Page as " - save as example.gcn3 in your current directory.

Make sure that you stay in the same directory as the files you downloaded.

### Practice sending and receiving messages
Next, we will use hop-client to send and receive messages.

To send a message, use hop.publish. View the command options with:
```
hop publish --help
```
Then, publish the message with:
```
hop publish kafka://dev.hop.scimma.org/$USER-topic -F config.conf example.gcn3
```
where `$USER` should be something unique to you (e.g., your local username).

To receive a message, use `hop subscribe`. View the command options with:
```
hop subscribe --help
```
Then, receive the message by subscribing with:
```
hop subscribe kafka://dev.hop.scimma.org/$USER-topic -F config.conf -e
```

## Continuously reading messages from a topic
Instead of reading old messages, `hop-client` can print new messages as they arrive.

To do this, subscribe to this announcement topic and wait for a message to arrive:
```
hop subscribe kafka://dev.hop.scimma.org/mma-announcement -F config.conf -t 120
```

You should see some messages print out over time.

To move on to the next section, press `Ctrl-C` to exit `hop-subscribe`.

## Session 2: Customizing hop-client

### Prepare your environment.
You'll need a working hop-client installation and your virtual environment from the first session.

To create a new app, install the Python cookiecutter package with either pip or conda:
* pip
  ```
  pip install cookiecutter
  ```
OR
* conda
  ```
  conda install --channel conda-forge cookiecutter
  ```

Use `cookiecutter` to set up the hop-client app template:
```
cookiecutter https://github.com/scimma/hop-app-template
```

Name your application "email" and press enter to skip the other entries. Go to the
directory of the app:
```
cd hop-email-app
```

### Edit files for custom app

To save us some typing, we've pre-created the edits you'd make yourself to create an app that receives messages from a topic stream and forwards them to an e-mail address of your choice. Use curl to download these (or visit the URL and "Save page as..."):
```
curl -o hop/apps/email/__main__.py https://raw.githubusercontent.com/scimma/may2020-techthon-demo/master/hop/apps/email/__main__.py
curl -o hop/apps/email/example.py https://raw.githubusercontent.com/scimma/may2020-techthon-demo/master/hop/apps/email/example.py
```

Install the app using pip:
```
pip3 install -e .
```

### Run the hop-email app
```
hop-email subscribe kafka://dev.hop.scimma.org/email-alerts -F ../config.conf -e -E your_email@address
```
You will receive the message an email at the address you specified with `-E`.
