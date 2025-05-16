# Features To Be Designed

IMPORTANT: Your job is to generate a technical design document for these features. DO NOT IMPLEMENT ANY CHANGES.

For example, if you are asked to create a technical design for feature 014, your output should ONLY be a technical design in a file called `IMPLEMENT-014.md`.

If you are unsure of something, ask the User. 

## Features

001. [ ] Create the directory structure for a new python project using 'src/' layout.
002. [ ] Identify the default XDG directories (e.g., XDG_DATA_HOME, XDG_CONFIG_HOME, XDG_CACHE_HOME) at startup
003. [ ] Use `structlog` for logging. 
    - Add `structlog` to the project
    - Configure logging to write log files in the default XDG cache or data directory 
    - Add log statements for key runtime events
    -  log rotation 
004. [ ] Create a sample configuration file called "sample.cfg"
005. [ ] Add a command line argument "--config" that accepts the full path to a configuration file
    - If this option is specified, do not attempt to load the default configuration file
006. [ ] Use python's standard `configparser` to read a configuration file. 
    - If the user has specified a configuration file using a command line argument, attempt to load that file using `configparser`. If that file is not found or there are any exceptions, log an ERROR to `stdout` and exit the program.
    - If the user has specified a configuration file using a command line argument, do not attempt to load the default configuration file. 
    - If the user has not specified a configuration file using a command line argument, load the configuration file `default.cfg` in the default XDG configuration directory; if that file is not found, log a warning and use fallback defaults
007. [ ] Add a configuration option to set the logging level
    - Default the logging level to "INFO"
    - Read logging configuration information from the 
    - Add command line arguments "-l" and "--log-level" to set the logging level
