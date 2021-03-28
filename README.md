# Deployment manual

This is the deployment manual for the Source Academy.

It is a work-in-progress, and is intended to:

- be publically usable and available
- describe a small range of deployment options
- be more structured and versioned than the old single Google Doc

We assume a reasonable level of familiarity with modern web infrastructure.

## Quick jump

- Frontend deployment
- Backend deployment
- Auxilliary services deployment

## High-level overview

The full Source Academy deployment as used for the CS1101S module in NUS SoC comprises these components:

\* denotes a mandatory component\
\# denotes a component that can be shared (e.g. you could use our deployment of that component)

### Components developed in-house

- [cadet-frontend](https://github.com/source-academy/cadet-frontend)*: the frontend. This is the part that users
  (students and staff) interact with. It is a standard React SPA written in TypeScript. One major dependency of the
  frontend is [js-slang](https://github.com/source-academy/js-slang), the implementation of our JavaScript subset
  language called Source. (It does not affect deployment, but it is good to know.)

  This can be deployed on any static site hosting service that supports SPAs (in particular, it must be able to rewrite non-existent paths to a 200 on index.html, as is needed for a SPA), such as Netlify, Vercel, Render, Surge, CloudFlare Pages, etc. GitHub Pages is not suitable as it does not allow you to rewrite non-existent paths. The CS1101S deployment uses Amazon CloudFront with Amazon S3.

- [cadet](https://github.com/source-academy/cadet)*: the main backend, sometimes referred to as the Elixir backend. This
  stores most of the dynamic data: the users, assessments, students' work, grading, etc. It is built on the Phoenix
  framework, written in Elixir.

  This can be deployed on any Linux server. The CS1101S deployment uses an Amazon EC2 instance.

- [grader](https://github.com/source-academy/grader): an AWS Lambda function that is called by the Elixir backend to
  autograde student submissions. Optional; without this, autograding of programming questions will not work. (MCQ
  questions are still autograded.)

  (It should not be too hard adapt the existing grader into an alternative, self-hosted solution for those who do not
  wish to use AWS. Contributions appreciated.)

- [modules](https://github.com/source-academy/modules)#: a repository of libraries that can be `import`ed into programs.
  This only needs any web hosting service; the CS1101S deployment uses GitHub Pages. It is possible to simply use our
  module repository at https://source-academy.github.io/modules/. Optional; without this, modules will not be available.
  (While some libraries such as the runes, curves and sounds library are currently bundled with the frontend, they will
  eventually be migrated to be modules.)

- [sharedb-ace-backend](https://github.com/source-academy/sharedb-ace-backend)#: a simple backend service that exposes a
  ShareDB database. This is used for the collaborative editor functionality. It is possible to simply use our instance;
  contact us for more information. Optional; without this, the collaborative editor will not be available.

  This can be deployed on any Linux server. The CS1101S deployment uses an Amazon EC2 instance.

### Off-the-shelf components

- PostgreSQL*: the main database where the Elixir backend stores its data. You can run your own instance or use a managed instance. The CS1101S deployment uses Amazon RDS.

- [YOURLS](https://github.com/YOURLS/YOURLS)#: the URL shortener service, used to provide the share function in the
  Playground. It is possible to simply use our instance; contact us for more information. Optional; without this, the
  share function in the Playground will not be available.

  This can be deployed on any service that supports PHP. It additionally requires a MariaDB (or MySQL) instance. The CS1101S deployment runs this using php-fpm, nginx and MariaDB on an EC2 instance.

### Cloud services

- Amazon S3: used to store game assets and Sourcecast audio files. Optional; without this, it will not be possible to
  use the game editor, or Sourcecasts. The game itself can still run if its assets are hosted elsewhere (any web host
  will do), but because the game editor uses the S3 API to upload files, as does the Sourcecasts feature, those will not
  work.

  (It should not be too hard to allow these features to work without S3 e.g. just uploading the files to the backend itself
  and serving it from there. Any contributions will be appreciated.)

- Amazon IoT: used as an MQTT broker for the remote execution functionality. Optional; without this, the remote
  execution functionality (i.e. robotics) will not work.

  (This only really needs modifications to the Elixir backend to support authenticating with a different, possibly
  self-hosted, MQTT broker. Again, any contributions will be appreciated.)

## Get started

Start off by deploying the frontend, followed by the backend, followed by auxilliary services.