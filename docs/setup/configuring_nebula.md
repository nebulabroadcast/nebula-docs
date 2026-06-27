Configuring Nebula
==================

Nebula configuration is managed through a settings template, which is a Python module that defines various configuration parameters for the system. This template allows you to customize Nebula's behavior to suit your specific deployment needs.

All settings templates are stored in the settings directory and you can apply them by running `make setup` in the root of the Nebula repository. 
This command will copy the settings template to the database and apply it to the system.

The following files are expected (but not required) to be present in the settings directory:

- `settings.py`: The main settings template that defines the configuration for the Nebula instance.
- `storages.py`: Defines the storage configurations for the Nebula instance.
- `services.py`: Defines the service configurations for the Nebula instance.
- `actions.py`: Defines actions such as transcoding, copying, or moving media files.
- `folders.py`: Defines what folders are available in the Nebula instance and their properties.
- `cs.py`: Additional classification schemes for assets, if needed.
- `meta_types.py`: Defines custom metadata types for assets, if needed.
- `views.py`: Defines custom views for browsing assets.
