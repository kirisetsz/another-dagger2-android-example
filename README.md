Dagger2 Example for Android
---------------------------

[![Build Status](https://travis-ci.org/kirisetsz/another-dagger2-android-example.svg?branch=master)](https://travis-ci.org/kirisetsz/another-dagger2-android-example)

IoC with dagger2 on Android.

The example contains an edited version of template project generated by Android Studio.

Build
-----

**Import the project into Android Studio is strongly recommended!**

Create a debug build:

    ./gradlew assembleDebug

Introduction
------------

The repository is my experimental work on making dagger2 more an dependency injection than dependency lookup framework in Android project.
IoC, more specifically the dependency injection framework can handle a lot of complexity passing and binding variables in an Android project.
Dagger2 is a fancinating work if one would like to implement dependency injection in their framework.

Most of tutorials introduce dagger2 on Android in a dependency lookup style:

    // Quote from: http://code.tutsplus.com/tutorials/dependency-injection-with-dagger-2-on-android--cms-23345

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        VehicleComponent component = Dagger_VehicleComponent.builder().vehicleModule(new VehicleModule()).build();
        vehicle = component.provideVehicle();
        Toast.makeText(this, String.valueOf(vehicle.getSpeed()), Toast.LENGTH_SHORT).show();
    }

That's meaningless and changes nothing to an `Activity`.

And some other article introduces an `inject` method in `@component`:

    // Quote from: https://guides.codepath.com/android/Dependency-Injection-with-Dagger-2

    @Singleton
    @Component(modules={AppModule.class, NetModule.class})
    public interface NetComponent {
        void inject(MainActivity activity);
        // void inject(MyFragment fragment);
        // void inject(MyService service);
    }

    public class MyActivity extends Activity {
        @Inject OkHttpClient mOkHttpClient;
        @Inject SharedPreferences sharedPreferences;
        public void onCreate(Bundle savedInstance) {
             // assign singleton instances to fields
             // We need to cast to `MyApp` in order to get the right method
             ((MyApp) getApplication()).getNetComponent()).inject(this);
        }
    }

That looks like a workaround with Android components but `MainActivity` implementation is coupled with the component.
Thus, basically not dependency injection.

By saying dependency injection, we are talking about [Inversion of Control (IoC)](https://en.wikipedia.org/wiki/Inversion_of_control)
which decouples dependency provider with dependency consumer. Dependency consumer have no knowledge about who provides the dependency.
And a container **not consumer itself** decides how to supply consumer with requested dependency, namely, inversion of control.

Obviously, dependency lookup is not an implementation of dependency injection (they are exclusive dependency strategy).
And in the second case, `MainActivity` as a dependency consumer have `NetComponent.inject(this)` which decides it's dependency,
not bad, but not the good answer :laughing:

Finally, I came up with the idea that `Activity` should be part of `@Module` to provide a dependency.
On the other hand, `Activity` should act like a container, because it's the entry of code in Android.
In this example project, an `ActivityDelegate` is defined. `ActivityDelegate` is the dependency consumer
and `Activity` is dependency provider or dagger `@Module`.

More detail can be found from source code.

_I'm a little confused with `@Scope` annotation and not sure if that's necessary in the example.
Welcome to discuss any problem in project's issue list._

Reference
---------

- [Dagger2 User's Guide](http://google.github.io/dagger/users-guide.html)

