## Data Stores

We want to make several data stores available through linked Docker containers:

1. [Redis](/data-stores-redis.html) - working now.
2. MySQL - in progress.
3. Memcached
4. PostgreSQL

### We have added some "magic" Dockerfile comments.

1. NO\_HTTP\_PROXY - tell octohost _not_ to add records to the reverse HTTP proxy - this shouldn't be accessible via HTTP.
2. ADD\_NAME - In order [to link containers together or use volumes from another container](https://docs.docker.io/en/latest/use/working_with_links_names/), you need to know the container's name. This tells octohost to name the container the same name as the git repo. This is a predictable name that won't clash with any other name because sites on an octohost are unique. \(Otherwise Docker gives them a unique random name.\)
3. VOLUMES\_FROM - To [use a volume from a data only container](https://docs.docker.io/en/latest/use/working_with_links_names/) this tells octohost to look for a container named "currentgitreponame\_data"
4. LINK\_SERVICE - To [link to a container with a running data store](https://docs.docker.io/en/latest/use/working_with_links_names/), this comment tells octohost to link to a container named 'currentgitreponame\_servicename'. If you were using Redis, and your git repo was called 'awesomewebsite', it would look for 'awesomewebsite\_redis' with the alias 'redis'. 