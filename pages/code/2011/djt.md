title: Creating a Custom Panel for the Django Debug Toolbar
tags: django, djt, debug, python, tips
date: 2011-03-11
published: true

Sometimes you work on stuff you don't really control, eg. when interacting with some mysterious SOAP server across the Internets, and you'd appreciate a little help from the Django ecosystem to ease debugging.

That's — you guessed it — my case currently, and I really appreciated being able to create my own custom panel for adding specific debugging capabilities to the awesome [Django Debug Toolbar](https://github.com/robhudson/django-debug-toolbar).

Here's how I did, learning mainly from the code of the panels shipping with the DJT. I'm supposing you have installed and configured the DJT in your project already.

First of all, create a `panels.py` module within one of your app (or wherever you want as it's in your python path) and create a DebugPanel derivated class, like this:

    from debug_toolbar.panels import DebugPanel
    from django.template.loader import render_to_string
    from django.utils.translation import ugettext_lazy as _


    class MyUsefulDebugPanel(DebugPanel):
        name = 'MyUseful'
        has_content = True

        def nav_title(self):
            return _('Useful Infos')

        def title(self):
            return _('My Useful Debug Panel')

        def url(self):
            return ''

        def content(self):
            context = self.context.copy()
            context.update({
                'infos': [
                    {'plop': 'plip'},
                    {'plop': 'plup'},
                ],
            })
            return render_to_string('panels/my_useful_panel.html', context)

The debug panel class methods and code should be self-explanatory enough. Just note you have to create a template, here `panels/my_useful_panel.html` to be stored in your project `templates` directory, with this kind of contents:

    <p>Hey, these are useful informations, I swear:</p>
    <ul>
    {% for info in infos %}
        <li>Plop is {{ info.plop}}</li>
    {% endfor %}
    </ul>

Now you have to register the new panel by adding its path to the `DEBUG_TOOLBAR_PANELS` tuple, in your `settings.py` (create it if it's not there):

    DEBUG_TOOLBAR_PANELS = (
        'debug_toolbar.panels.version.VersionDebugPanel',
        'debug_toolbar.panels.timer.TimerDebugPanel',
        'debug_toolbar.panels.settings_vars.SettingsVarsDebugPanel',
        'debug_toolbar.panels.headers.HeaderDebugPanel',
        'debug_toolbar.panels.request_vars.RequestVarsDebugPanel',
        'debug_toolbar.panels.template.TemplateDebugPanel',
        'debug_toolbar.panels.sql.SQLDebugPanel',
        'debug_toolbar.panels.signals.SignalDebugPanel',
        'debug_toolbar.panels.logger.LoggingPanel',
        'my_app_name.panels.MyUsefulDebugPanel',
    )

Of course it's up to you to put really useful informations there, but here's the result, tadaa:

![result screenshot](http://media.tumblr.com/tumblr_lhfpagXiif1qbt2jc.png)

Thanks for reading, happy ponying.
