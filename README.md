# Virtual Chat agent
This docker-compose repository should set up a collection of working Moodle and solr containers that will run the Virtual Chat Agent demonstrator.

## Installation
1. Clone this repository.
2. Run `docker-compose build`. This should build moodle, mysql and solr images.
3. Run `docker-compose up` to bring up 3 containers.

## Solr installation.
1. Once the solr server is runnng, access the command line via `docker exec -it server-solr-1 bash`
2. On the command line run `solr create -c moodle` to create the Moodle collection.
3. From the `/var/solr/orig` copy the configuration files over the Moodle collection configuration. 

    This will add in the configuration setings for the DenseVectorField types and the configuration for the Tika extraction library.

    `cp -r /var/solr/orig/ /var/solr/data/moodle/conf/`
4. Restart solr. This is easiest done by doing a `docker-compose down` and `docker-compose up`.


## Moodle Installation
1. Access `http://localhost:8080` and run the Moodle installation, or acccess the docker container and run `php /var/www/html/admin/cli/install_database.php`

### AI Provider configuration
An AI Provider will need to be configured.

1. Access the `local_ai` AI Providers settings at `http://localhost:8080/admin/category.php?category=local_ai_settings`, or via the Local Plugins section.
2. Select the `Providers` option.
3. Click on the `OpenAI API Based Provider`
4. Provide a `Name`. This will be displayed to users selecting an AI provider.
5. Set the `Base URL`. For OpenAI this will be `https://api.openai.com`
6. Set your `API Key`. This is configured on the [Open AI platform ](https://platform.openai.com).
    1. Access OpenAI platform
    2. Select a Project from the dropdown
    3. Select the `Dashboard` option from the top right menu
    4. Select `API Keys` from the left hand menu.
    5. Use th "Create new secret key` button in the top right to create new key.
7. Expand `Features`
8. Check the `Allow Chat` option
9. Set the `Completions path` option. This is the *relative* URL to the Base URL to text completion / chat API. For OpenAI this is `/v1/chat/completions`.
10. Set the `Completion Model` to an appropriate value for the model you wish to use. `gpt-4o` is the current latest Open AI model.
11. Enable the `Allow Embeddings`
12. Set the `Embeddings path` option. This is the *relative* URL to the Base URL to generate an embedding from text. For OpenAI this is `/v1/embeddings`.
13. Set the `Embedding model` to appropriate text embedding model. For OpenAI this is `text-embedding-3-large` or `text-embedding-3-small`

The `Content Constraints` section allows you to select a Moodle Category or Course that this provider will be available in.

If you do not select anything this provider instance will be accessible throughout your site.

### Global Search Configuration
Once basic Moodle is installed, you need to configure the Global Search.

1. Access the global search configuration page [http://localhost:8080/admin/settings.php?section=manageglobalsearch](http://localhost:8080/admin/settings.php?section=manageglobalsearch)
2. Select the `Solr for Rag` search engine. This should have been pulled from [https://github.com/mhughes2k/moodle-search_solrrag](https://github.com/mhughes2k/moodle-search_solrrag) as part of the docker build of the moodle image.
3. Click on the `Save Changes` button.
4. The `3. Set up Search engine` option should display "no solr configuration found".
3. Click on the `3. Setup search engine` option.
4. Under `AI Settings`, set the `Choose Provider` option to an AI Provider that has been defined. A global provider is probably best.
5. For `Choose File Content extractor` choose `Solr with internal tika`. 

    A standalone tika server is not fully implemented.
6. Under `Connection Settings`, set the `Host name` to `solr`.
7. Set the `Index name` to `moodle`. Unless you changed this in Step 2 of the solr configuration.
8. Click `Save changes`.
9. Return to the `Manage global search` configuration page.
10. You should have all "green" statuses for Steps 1 - 3.
11. Access Step 5 `Enable global search`.
12. Set `Enable global search` to "Yes".
13. If your cron is running on a regular basis (as it should), the global search indexer should now start indexing content in moodle sites.
    
    You can run `php search/cli/indexer.php` within the moodle container to trigger the indexing process as well.

## Next Steps
1. Restore or create a course
2. Create an instance of the "AI Chat (Reference)" activity.
3. Give it a name.
4. In `AI Provider` select an AI provider. 
    This doesn't have to be the same AI Provider that the search uses. This provider is used to synthesise the response to the user.

5. You should get a prompt and be able to interrogate your course.