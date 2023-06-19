# Server requirements

### Hardware requirements

- _Minimum (up to 25 concurrent users)_

|                |                                                          |
|----------------|----------------------------------------------------------|
| **Processor**  | Intel Xeon E5440 @ 2.83GHz                               |
| **Memory**     | 4 GB                                                     |
| **Disk space** | 20 GB (3 GB for database files + enough for attachments) |
| **Other**      | Network card is required                                 |

- _Larger implementations_

| Users | Threads / vCPU | RAM   | HDD   |
|-------|----------------|-------|-------|
| 50    | 8              | 12GB  | 40GB  |
| 100   | 12             | 32GB  | 60GB  |
| 200   | 24             | 64GB  | 200GB |
| 500   | 24             | 128GB | 500GB |

> Values in the table are not strictly given and the whole configuration depends on the extent and significance of the
> use of Easy Redmine in the company. As well as on the frequency of user access and requests sent to the server.

### Software requirements

|              |                                       |
|--------------|---------------------------------------|
| **OS**       | Debian 10 or 11 on amd64 architecture |
| **Redmine**  | upgradable from version 5.0.4         |
| **Ruby**     | 3.1.2                                 |
| **Bundler**  | 2.3.7+                                |
| **Rubygems** | 3.3.x                                 |
| **Database** | Percona 8.x                           |
| **Redis**    | 5.x                                   |
| **NodeJS**   | 18.8                                  |

### Internet connection requirements

The connection from the user to the server where Easy Redmine is located must be at least **10 Mbit**. You need to take
into
account the whole route from the user to the server, so with geographically distant locations, the response of the
server may take a longer time, regardless of the user's declared internet connection from their provider. The bandwidth
of the server is also a factor.

### Supported Browsers

- Chrome
- Firefox
- Opera
- Safari
- Microsoft Edge
