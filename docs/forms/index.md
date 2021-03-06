## Using the Prism.Forms.Extended

The Prism.Forms.Extended package is designed to make it even easier for you to integrate these fantastic packages. As you'll see using it is identical in every way to creating a typical Prism Application. The only difference is that you are installing the Prism.Forms.Extended package instead of Prism.Forms, Prism.DryIoc.Forms or Prism.Unity.Forms.

```xml
<prism:PrismApplication xmlns="http://xamarin.com/schemas/2014/forms"
                        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                        xmlns:prism="http://prismlibrary.com"
                        x:Class="Contoso.Awesome.App">
</prism:PrismApplication>
```

```c#
public partial class App
{
    protected override void OnInitialized()
    {
        InitializeComponent();
        NavigationService.NavigateAsync("MainPage");
    }

    protected override void RegisterTypes(IContainerRegistry containerRegistry)
    {
        // Register your services like normal
        containerRegistry.RegisterForNavigation<MainPage>();
    }
}
```

!!! danger "Critical Notice"
    The Prism Application in `Prism.Forms.Extended` requires the use of a container implementation from the Prism.Container.Extensions project. It will automatically wire in any reference without any changes to code. You simple need to ensure that Prism.DryIoc.Extensions, Prism.Microsoft.DependencyInjection.Extensions, or Prism.Unity.Extensions is installed.

### Why use Prism.Forms.Extended

The extended PrismApplication is cross compiled for Xamarin.iOS and Xamarin.Android and provides several out of the box improvements over the normal PrismApplication.

- It provides you all of the container extensions found here that help make advanced registration scenarios much easier.
- It includes the `ILogger` from [Prism.Plugin.Logging](http://logging.prismplugins.com)
    - By default the [`AggregateLogger`](http://logging.prismplugins.com/aggregate-logger/) is registered and is available.
- It has pre-wired support for logging XAML errors and other issues directly from Xamarin.Forms (It will use your ILogger to log things like Bindings that cannot be resolved)
- Becase it is cross compiled, it has global Exception Handling built in for:

| Environment | Platform |
|-------------|----------|
| AndroidEnvironment | Android |
| ObjectiveC.Runtime | iOS |
| AppDomain | All |
| TaskScheduler | All |

#### Setting up the Aggregate Logger

By default the AggregateLogger will have the ConsoleLogger added ensuring that any logging output will always be written to the Console. This is generally exactly what you want for development, and in Production this is often helpful as well as you can still view the log on your device with a build from the Google Play / App Store. Chances are though that you will want to add additional loggers. To do you this you will want to do the following:

```c#
protected override void OnInitialized()
{
    var logger = Container.Resolve<IAggregateLogger>();

    // Add another logging implementation from the Logging Plugin
    // or that you have implemented
    logger.AddLogger(Container.Resolve<SyslogLogger>());
}
```