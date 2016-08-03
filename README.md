Simple Preferences
===

[![Android Arsenal](https://img.shields.io/badge/Android%20Arsenal-Simple%20Preferences-brightgreen.svg?style=flat)](http://android-arsenal.com/details/1/3233)

Simple Preference is a SharedPreference Manager generator.

The library generates code for managing SharedPreferences at compile time with Annotation Processor.  
So you don't need to manage SharedPreferences' key string by your hand anymore.

And because getter/setter is generated by Annotation Processor, you can take full advantage of AndroidStudio's auto completion.

Currently the library supports `get`/`set`/`has`/`remove` operation for each keys(`String`/`int`/`float`/`long`/`Set<String>`), and `clear` operation for SharedPreference itself.


## Installation

This library requires JDK8 to run annotation processor.

```groovy
// distributed via jCenter
buildscript {
  dependencies {
    classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
  }
}

apply plugin: 'com.neenbedankt.android-apt'

dependencies {
  apt 'net.yslibrary.simplepreferences:simplepreferences-processor:1.2.0'
  compile 'net.yslibrary.simplepreferences:simplepreferences:1.2.0'
}
```

## Usage

define ordinary POJO with `@Preferences` and `@Key`.

```java
@Preferences
public class Config {
  // you'd better define these variable as protected to avoid accidental access
  @Key
  protected int userId = 0;

  @Key
  protected String userName = "";

  @Key(omitGetterPrefix = true)
  protected boolean isPremium = false;
}

```


and following class is generated

```java
public class ConfigPrefs extends Config {
  private final SharedPreferences prefs;

  private ConfigPrefs(@NonNull Context context) {
    prefs = context.getApplicationContext().getSharedPreferences("config", Context.MODE_PRIVATE);
  }

  public static ConfigPrefs create(@NonNull Context context) {
    if (context == null) {
      throw new NullPointerException("Context is Null!");
    }
    return new ConfigPrefs(context);
  }

  public void clear() {
    prefs.edit().clear().apply();
  }

  public ConfigPrefs setUserId(int value) {
    prefs.edit().putInt("user_id", value).apply();
    return this;
  }

  public int getUserId() {
    return prefs.getInt("user_id", userId);
  }

  public boolean hasUserId() {
    return prefs.contains("user_id");
  }

  public ConfigPrefs removeUserId() {
    prefs.edit().remove("user_id").apply();
    return this;
  }

  public ConfigPrefs setUserName(String value) {
    prefs.edit().putString("user_name", value).apply();
    return this;
  }

  public String getUserName() {
    return prefs.getString("user_name", userName);
  }

  public boolean hasUserName() {
    return prefs.contains("user_name");
  }

  public ConfigPrefs removeUserName() {
    prefs.edit().remove("user_name").apply();
    return this;
  }

  public ConfigPrefs setIsPremium(boolean value) {
    prefs.edit().putBoolean("is_premium", value).apply();
    return this;
  }

  public boolean isPremium() {
    return prefs.getBoolean("is_premium", isPremium);
  }

  public boolean hasIsPremium() {
    return prefs.contains("is_premium");
  }

  public ConfigPrefs removeIsPremium() {
    prefs.edit().remove("is_premium").apply();
    return this;
  }
}
```

As you may noticed from generated getter method, the values you set in your POJO become default values for each keys.

Generated classes will be placed at the same package as parent classes.

## Annotations

### `@Preferences`

Declare annotated class as SharedPreferences model.

| parameter | description | default vale |
|---|---|---|
| `value` | SharedPreferences name | empty (class name is converted to lower_snake_case and used as SharedPreferences name) |
| `useDefault` | use DefaultSharedPreferences or not | false |


### `@key`

Declare annotated variable as SharedPreferences key.

| parameter | description | default vale |
|---|---|---|
| `name` | preference's key name | empty (variable name is converted to lower_snake_case and used as key) |
| `omitGetterPrefix` | whether or not prepend prefix for getter method | false (prepend prefix) |
| `needCommitMethod` | whether or not create additional setter method which use SharedPreferences.Editor#commit() | false(disabled) |

`omitGetterPrefix` is useful when you define boolean value.
`needCommitMethod` is useful when you want to write to the backing file synchronously.


License
-------

    Copyright 2016 Shimizu Yasuhiro (yshrsmz)

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
