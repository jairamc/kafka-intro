# Introduction to Kafka

Presentation introducing basic concepts and features in Kafka

## Installation

The **basic setup** is for authoring presentations only. The **full setup** gives you access to all reveal.js features and plugins such as speaker notes as well as the development tasks needed to make changes to the source.

### Basic setup

The core of reveal.js is very easy to install. You'll simply need to download a copy of this repository and open the index.html file directly in your browser.

1. Clone the repository
   ```sh
   $ git clone git@github.com:jairamc/kafka-intro.git
   ```

2. Open `index.html` in a browser to view it


### Full setup

Some reveal.js features, like external Markdown and speaker notes, require that presentations run from a local web server. The following instructions will set up such a server as well as all of the development tasks needed to make edits to the reveal.js source code.

1. Install [Node.js](http://nodejs.org/) (4.0.0 or later)

1. Clone the repository
   ```sh
   $ git clone git@github.com:jairamc/kafka-intro.git
   ```

1. Navigate to the `kafka-intro` folder
   ```sh
   $ cd kafka-intro
   ```

1. Install dependencies
   ```sh
   $ npm install
   ```

1. Serve the presentation and monitor source files for changes
   ```sh
   $ npm start
   ```

1. Open <http://localhost:8000> to view your presentation

   You can change the port by using `npm start -- --port=8001`.


### Folder Structure
- **css/** Core styles without which the project does not function
- **js/** Like above but for JavaScript
- **plugin/** Components that have been developed as extensions to reveal.js
- **lib/** All other third party assets (JavaScript, CSS, fonts)


## License

MIT licensed

Original work copyright (C) 2016 Hakim El Hattab, http://hakim.se
Modified work Copyright 2017 Jairam Chandar, http://jairam.me
