SlmQueueAmq
==================

[![Build Status](https://travis-ci.org/juriansluiman/SlmQueueAmq.png?branch=master)](https://travis-ci.org/juriansluiman/SlmQueueAmq)
[![Latest Stable Version](https://poser.pugx.org/slm/queue-amq/v/stable.png)](https://packagist.org/packages/slm/queue-amq)
[![Latest Unstable Version](https://poser.pugx.org/slm/queue-amq/v/unstable.png)](https://packagist.org/packages/slm/queue-amq)


Created by Jurian Sluiman


Requirements
------------
* [Zend Framework 2](https://github.com/zendframework/zf2)
* [SlmQueue](https://github.com/juriansluiman/SlmQueue)


Installation
------------

First, install SlmQueue ([instructions here](https://github.com/juriansluiman/SlmQueue/blob/master/README.md)). Then,
add the following line into your `composer.json` file:

```json
"require": {
    "slm/queue-amq": "0.4.*"
}
```

Then, enable the module by adding `SlmQueueAmq` in your application.config.php file. You may also want to
configure the module: just copy the `slm_queue_amq.local.php.dist` (you can find this file in the config
folder of SlmQueueAmq) into your config/autoload folder, and override what you want.


Documentation
-------------

Before reading SlmQueueAmq documentation, please read [SlmQueue documentation](https://github.com/juriansluiman/SlmQueue).

(Don't forget to first install Active MQ, and to run the daemon program on the server)


### Setting the connection parameters

Copy the `slm_queue_amq.local.php.dist` file to your `config/autoload` folder, and follow the instructions.


### Adding queues

A concrete class that implements the SlmQueue interface for Active MQ is included
as `SlmQueueAmq\Queue\AmqQueue` and a factory is available to create the queue.
Therefore, if you want to have a queue called "email", just add the following line in your
`module.config.php` file:

```php
return array(
    'slm_queue' => array(
        'queue_manager' => array(
            'factories' => array(
                'email' => 'SlmQueueAmq\Factory\AmqQueueFactory'
            )
        )
    )
);
```

This queue can therefore be pulled from the QueuePluginManager class.


### Operations on queues

#### push

> Note these options are not implemented at this moment!

Valid options are:

* delay: the delay in milliseconds before a job become available to be popped (defaults to no delay)
* period: in milliseconds, how much time a job can be running for before it's put back into the queue
* repeat: the number of times the job should be repeatedly available (defaults to 1, no repeating jobs)
* cron: a CRON string to schedule the job via cron

Example:

```php
$queue->push($job, array(
    'cron'     => '0 * * * *',
    'delay'    => 1000,
    'period'   => 1000,
    'repeat'   => 9
));
```

The above code will deliver the job 10 times, with a one second delay between
each job, and this will happen every hour. See more explaination about the
options in the [Active MQ manual](http://activemq.apache.org/nms/stomp-delayed-and-scheduled-message-feature.html).

#### pop

Valid option is:

* timeout: by default, when we ask for a job, it will block until a job is found (possibly forever if new jobs never
come). If you set a timeout (in seconds), it will return after the timeout is expired, even if no jobs were found

### Executing jobs

SlmQueueAmq provides a command-line tool that can be used to pop and execute jobs. You can type the following
command within the public folder of your Zend Framework 2 application:

`php index.php queue amq <queue> [--timeout=]`

The queue is a mandatory parameter, while the timeout is an optional flag that specifies the duration in seconds
for which the call will wait for a job to arrive in the queue before returning (because the script can wait forever
if no job come).
