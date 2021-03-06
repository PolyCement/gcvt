# Getting embedding working in WordPress

Don't need to mess around with HTML. WP supports automatic embedding if writer just pastes the page URL into the block.

https://wordpress.org/support/article/embeds/#adding-support-for-an-oembed-enabled-site - might be able to get away without registering if we provide an iframe (or if WP wraps it in one - unclear)
https://codex.wordpress.org/Function_Reference/wp_oembed_add_provider

https://oembed.com/ - looks quite simple. Particularly of interest is sect 4 - Discovery and 2.3.4.4 - The rich type. Once we have proper domain and it running we can register to be official provider and it should work in quite a few places.

## Notes on oEmbed provider implementation

Might be worth making WSGI compliant http://wsgi.tutorial.codepoint.net/parsing-the-request-get which would let us use better webserver than `http.server` (e.g. gunicorn)

[This](https://medium.com/@daniel.carlier/how-to-build-a-simple-flask-restful-api-with-docker-compose-2d849d738137) seems like it might be useful.

### Completed stuff

Have written a small python web server to deal with requests. To deploy it, we should probably use Docker so we can have an if not all-in-one, we can have a most-stuff-in-one. It should be more tightly coupled to the vis tool than WordPress as I imagine WordPress will be replaced quicker than the vis tool.

We will need a router (probably just [Traefik](https://github.com/containous/traefik)) to direct stuff to the various servers.

# R stuff

Should probably base on https://hub.docker.com/r/rocker/shiny-verse and follow https://ropenscilabs.github.io/r-docker-tutorial/05-dockerfiles.html

# Development loop

E.g. `ls **py | sudo entr -r docker-compose up --build` <- still quite slow but not unbearable

# Deploying this

#. Stop all containers
#. Back-up volumes (persistent data) from local host via https://github.com/loomchild/volume-backup 
    #. See https://github.com/loomchild/volume-backup#user-content-copy-volume-between-hosts esp
    #. `docker volume ls` will show you the ones you need
#. Clone this repo
#. Change URL in WordPress before moving deployment - otherwise it's a pain e.g. https://codeblog.dotsandbrackets.com/migrate-wordpress-docker/#crayon-5ddc34561323d649039809
#. Copy .env and backend/data to new box securely
#. `docker-compose up --build -d` on new box
#. Change URL in wordpress via admin panel
