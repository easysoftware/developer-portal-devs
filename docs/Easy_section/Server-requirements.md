# Server requirements

### Minimum hardware requirements

|                |                            |
|----------------|----------------------------|
| **Processor**  | Intel Xeon E5440 @ 2.83GHz |
| **Memory**     | 6 GB                       |
| **Disk space** | 120 GB                     |
| **Other**      | Network card is required   |

### Software requirements

|              |                                 |
|--------------|---------------------------------|
| **OS**       | Debian 11 on amd64 architecture |
| **Redmine**  | upgradable from version 5.0.4   |
| **Ruby**     | 3.1.2                           |
| **Bundler**  | 2.3.7+                          |
| **Rubygems** | 3.3.x                           |
| **Database** | Percona 8.x                     |
| **Redis**    | 5.x                             |
| **NodeJS**   | 18.8                            |

### Requirements for running Docker

|                    |          |
|--------------------|----------|
| **Docker**         | 20.10.22 |
| **Docker Compose** | 2.15.1   |

### Internet connection requirements

The connection from the user to the server where the app is located must be at least **10 Mbit/s**. You need to
take into account the whole route from the user to the server, so with geographically distant locations, the response of
the server may take a longer time, regardless of the user's declared internet connection from their provider. The
bandwidth of the server is also a factor.

### Supported Browsers

- Chrome
- Firefox
- Opera
- Safari
- Microsoft Edge
