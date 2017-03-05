# Android Day 4: Git, Multiple Activities

Author: Philipp Haller

## Importing an Android project into a Git repository

Android Studio creates a lot of project files, which are only needed
locally to build and run an application. Many of these project files
do not need to be added to a Git repository, since it is enough to
create them only once locally, when importing a project into Android
Studio.

However, certain project and build files must be added to the Git
repository, because they define the Android project structure that is
required for importing a project into Android Studio. These essential
build files are:

    build.gradle
    gradle.properties
    settings.gradle
    app/build.gradle
    app/src

Note that `app/src` is the directory which contains the source code of
the application, resources, unit tests, etc.

For a simple app, the following `.gitignore` is suitable:

    *.iml
    .gradle
    /local.properties
    /.idea/workspace.xml
    /.idea/libraries
    .DS_Store
    /build
    /captures
    .externalNativeBuild
    app/build
    .idea
    gradle
    gradlew
    gradlew.bat


## Exercise 1

Import the Android application you created as part of the exercises of
day 2 into a new Git repository. Make sure to import only those files
required for other developers to import your project into Android
Studio.


## Creating Additional Activities

An *activity* essentially corresponds to one "screen" of an Android
app. Most apps switch between multiple screens. For example, an app
might provide a different screen for configuring user preferences, or
it might open a temporary screen for making a selection. Such apps
thus require the creation of multiple activities, and there needs to
be ways to transfer data and results between different activities.

To create an additional activity in Android Studio choose File -> New
-> Activity -> Empty Activity. This (a) creates a Java source file
under `app/src/main/java`, (b) creates an XML file under
`app/src/main/res/layout`, and (c) extends
`app/src/main/AndroidManifest.xml`.

If you would like to make a newly created activity the activity that
your app should start with, then you need to modify
`app/src/main/AndroidManifest.xml` accordingly, by changing the name
of the activity that is defined with the following XML element:

    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>


### Switching Between Activities

To switch between activities, we first need to create a so-called
"intent". An intent carries all information required by the Android
runtime to start a new activity. If we do not need to transfer any
data to the activity we would like to start, then a simple intent is
sufficient:

    Intent intent = new Intent(this, CreateEventActivity.class);
    startActivity(intent);

The first argument passed to the constructor of `Intent` is the
current activity, `this`. Thus, here we assume that the above code is
executed from within an `Activity` class. The second argument passed
to the constructor of `Intent` identifies the class of the activity we
would like to start, here class `CreateEventActivity`. Note that for a
given class `C`, `C.class` returns an object of type
`java.lang.Class`. Such a `Class` instance is a *description* of the
corresponding Java class; it can be used to create new instances of
the class, enumerate the fields and methods of the class, and
more. (The `java.lang.Class` type is part of Java's so-called
*reflection* API.)

Note: more reading on [activities in
Android](https://developer.android.com/guide/components/activities/index.html).


### Returning Results From Activities

Often we would like to start an activity which produces a result that
should be used in its parent activity subsequently. To support such
use cases, Android provides an alternative to `startActivity` called
`startActivityForResult`. The main difference between the two is that
the latter method takes an additional argument which is the code of a
request. This request code can be chosen arbitrarily--it is only used
to identify the original request whenever a result is received from
the child activity. It is common to define a constant for each request
code:

    public class MainActivity extends AppCompatActivity {

        static final int PICK_DATE_REQUEST = 0;

        public void onNewEvent(View view) {
            Intent intent = new Intent(this, CreateEventActivity.class);
            startActivityForResult(intent, PICK_DATE_REQUEST);
        }

        ...
    }

When the child activity is ready to return a result to its parent
activity, it creates an intent itself, and declares the intent to be
its result. Finally, the activity calls `finish` which terminates the
activity and returns to the parent activity:

    Intent data = new Intent();
    data.putExtra("date", tv.getText());
    setResult(Activity.RESULT_OK, data);
    finish();

In the above listing, we pass `Activity.RESULT_OK` as the first
argument to `setResult` to indicate that the activity returns normally
with the result. The result code `Activity.RESULT_CANCELED` can be
used to indicate that the action producing the result was cancelled.

On the side of the parent activity, the result is received using the
`onActivityResult` callback method:

    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        if (requestCode == PICK_DATE_REQUEST) {
            if (resultCode == RESULT_OK) {
                Toast t = Toast.makeText(this, data.getStringExtra("date"), Toast.LENGTH_SHORT);
                t.show();
            }
        }
    }

The `onActivityResult` callback method is called for *all*
result-returning child activities. Therefore, the above implementation
first checks whether `onActivityResult` carries the result of an
activity started using the `PICK_DATE_REQUEST` code. Furthermore, it
checks whether the activity was completed successfully (`resultCode ==
RESULT_OK`).


## Exercise 2

Change your solution to exercise 4 from day 2 such that a new activity
is started when the user selects the kind of measurement (distance,
weight, or volume). All other selections and the conversion should
then be made in the newly started activity.

## Exercise 3

- Create a small app for entering reminders for events.

- Create two activities. The main activity should provide a button for
  entering a new event. When this button is clicked, a new activity
  should be started for entering the details of the new event such as
  the date, description, etc.

- The second activity should at least provide the possibility to
  select a date. For selecting a date, you should use a
  `DatePickerDialog`. The Android API [guide on
  pickers](https://developer.android.com/guide/topics/ui/controls/pickers.html)
  shows how to create a date picker using a `DialogFragment`.

- The second activity should provide a button that, when clicked,
  returns the selected date (and perhaps more information about the
  event) to the first activity. For the transfer of the result,
  utilize `startActivityForResult`, `setResult`, and `finish`, among
  others.
