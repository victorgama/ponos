# Ponos 🔨

**Ponos** schedules ActiveJob tasks based on a Cron model

## Installing

Ponos is distributed as a Docker image:

```
docker pull victorgama/ponos:latest
```

The image requires a configuration file to be available in `/app/ponos.toml`,
and a connection to your local Redis server used by Sidekiq (or any other task
runner)

## Configuring

Below is a sample `ponos.toml` configuration:

```toml
# This file is used to configure and set Ponos' schedule
# Please do keep in mind that Ponos does not use the regular
# crontab format as expressed in the next diagram:
#
#    *  *  *  *  *
#    |  |  |  |  \_> Day of Week (0-6)
#    |  |  |  \_> Month (0-12)
#    |  |  \_> Day of Month (0-31)
#    |  \_> Hour (0-23)
#    \_> Minute (0-59)
#
# Ponos contains an extra field representing seconds, as expressed below:
#    *  *  *  *  *  *
#    |  |  |  |  |  \_> Day of Week (0-6)
#    |  |  |  |  \_> Month (1-12)
#    |  |  |  \_> Day of Month (1-31)
#    |  |  \_> Hour (0-23)
#    |  \_> Minute (0-59)
#    \_> Second (0-59)
#

driver = 'sidekiq-activejob'

[redis]
url = 'redis://127.0.0.1:6379/1'

[schedule]
    [schedule.notify_rejected_refunds]
    period = '* * * * * *'
    job =    'TestJob'
```

Currently, the only driver available is `sidekiq-activejob`, which enqueues jobs
to Sidekiq using structures generated by ActiveJob. The driver requires a
`redis` section to be defined with a `url` key.

`schedule` represents the schedule Ponos will execute. Inside it we find
subsections representing each job, and its properties:

- `period`: Required. Determines when the job will be enqueued
- `job`: Required. Name of the Job, just as implemented in your application.
- `retry`: Optional. Determines whether or not the job should be retried in case
of errors. Default: `false`
- `queue`: Optional. Name of the queue in which this job is executed. Default:
`"default"`

> **Protip**: The subsection name identifies the job inside Ponos and must be
unique.

## License

```
MIT License

Copyright (c) 2018 Victor Gama de Oliveira

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
