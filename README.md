# CLASSe project demonstrator

This project provides a demonstrator for Moonshot + ERP optimisation developed during the the CLASSe project: https://www.um.es/classe/

## Overview

The demonstrator is composed of three different services:

* Home RADIUS. The home server where the user authenticates. REALM: `home.org`
* Visited RADIUS. The visited server the user wants to authenticate locally after having bootstrapped ERP credentials. REALM: `visited1.org`
* erp_client. A combination of Moonshot client and Moonshot service, connected to the Visited RADIUS.

## How to use it

* Build and start the services. You can also launch each of them on a different terminal, to have a separated log output. In that case, make sure you start the visited RADIUS last, since it needs home RADIUS to be running first or the DNS name won't resolve.

  ```bash
  docker-compose up --build --force-recreate
  ```

* Launch the first moonshot authentication.

  ```bash
  docker-compose exec erp_client gss-client -spnego erp_client a@service hello
  ```

  * Select the Test User credential and click on Send
  * The authentication happens through the visited and home RADIUS servers. This is the *Implicit Bootstrapping* scenario.

* Launch a second Moonshot authentication. This time ERP authentication happens only through the visited RADIUS server. This is the *ERP Local Operation*



