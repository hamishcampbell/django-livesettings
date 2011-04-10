Usage
=====

Creating Config.py
------------------

In order to use livesettings, you will need to create a :file:`config.py` in your django application. For this example, we will create a :file:`config.py` file in the 'myapp' directory.

For this specific app, we want to allow an admin user to control how many images are displayed on the front page of our site. We will create the following :file:`config.py`::

    from livesettings import config_register, ConfigurationGroup, PositiveIntegerValue, MultipleStringValue
    from django.utils.translation import ugettext_lazy as _

    # First, setup a grup to hold all our possible configs
    MYAPP_GROUP = ConfigurationGroup('MyApp', _('My App Settings'), ordering=0)

    # Now, add our number of images to display value
    # If a user doesn't enter a value, default to 5
    config_register(PositiveIntegerValue(
        MYAPP_GROUP,
            'NUM_IMAGES',
            description = _('Number of images to display'),
            help_text = _("How many images to display on front page."),
            default = 5
        ))

    # Another example of allowing the user to select from several values
    config_register(MultipleStringValue(
            MYAPP_GROUP,
            'MEASUREMENT_SYSTEM',
            description=_("Measurement System"),
            help_text=_("Default measurement system to use."),
            choices=[('metric',_('Metric')),
                        ('imperial',_('Imperial'))],
            default="imperial"
        ))

In order to activate this file, add the following line to your :file:`models.py`::

    import config
    
You can now see the results of your work by running the dev server and going to `settings <http://127.0.0.1:8000/settings/>`_ ::

    python manage.py runserver

Accessing your value in a view
------------------------------

Now that you have been able to set a value and allow a user to change it, the next step is to access it from a view. 

In a :file:`views.py`, you can use the config_value function to get access to the value. Here is a very simple view that passes the value to your template::


    from django.shortcuts import render_to_response
    from livesettings import config_value

    def index(request):
        image_count = config_value('MyApp','NUM_IMAGES')
        # Note, the measurement_system will return a list of selected values
        # in this case, we use the first one
        measurement_system = config_value('MyApp','MEASUREMENT_SYSTEM')
        return render_to_response('myapp/index.html', 
                                {'image_count': image_count,
                                'measurement_system': measurement_system[0]})

Using the value in your :file:`index.html` is straightforward::

    <p>Test page</p>
    <p>You want to show {{image_count}} pictures and use the {{measurement_system}} system.</p>


Next Steps
----------

The rest of the various livesettings types can be used in a similar manner. You can review the `satchmo code <https://bitbucket.org/chris1610/satchmo/src>`_ for more advanced examples.

.. _`Django-Keyedcache`: http://bitbucket.org/bkroeze/django-keyedcache/
.. _`Satchmo Project`: http://www.satchmoproject.com
.. _`pip`: http://pypi.python.org/pypi/pip