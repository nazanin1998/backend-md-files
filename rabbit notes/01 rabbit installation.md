# Rabbit Intallation

Source: https://www.rabbitmq.com/install-windows.html


In this part we consider installing RabbitMQ on windows.
## 1. Installing RabbitMq Options

We have  two recommended installation options:

1. Using Chocolatey
2. Using the official installer as an administrative user

## 1-1. Using chocolatey
The Chocolatey RabbitMQ package is open source.

To install RabbitMQ using Chocolatey, run the following command from the command line

```
choco install rabbitmq
```
__Note__: Chocolatey is the optimal installation method.

---------------------
## 1-2. Using the official installer 

Prerequires:

1. Erlang must be installed using an administrative account.
2. It is highly recommended that RabbitMQ is also installed as an administrative account.
3. Installation path must only contain ASCII characters. It is highly recommended that the path does not contain spaces in any directory names.

### 1-2-1. Installation Steps
1. First of all, download and install Erlang/Otp.
2. Download windows install of RabbitMQ.

## 2. Enable Web Interface of RabbitMQ
1. Run _RabbitMQ start_ runner.
2. Run _RabbitMQ command line_ runner and write below command:

```
rabbitmq-plugins enable rabbitmq_management
```
3. Run _RabbitMq reinstall_ runner.
4. Restart system.
5. See the RabbitMQ Managment in:
```
http://localhost:15672/
```
Note: open it by _firefox_.
6. Default username and password are:
```
username = guest
password = guest
```
