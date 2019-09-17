# Navigation
## Navigation components
#### 1. Adding the Navigation Components to the Project
Within the **Project** build.gradle:
```xml
buildscript {
    ext {
        ...
        version_navigation = '1.0.0'
        ...
    }
```
Within the **app** build.gradle:
```xml
dependencies {
    ...
    implementation "android.arch.navigation:navigation-fragment-ktx:$version_navigation"     
    implementation "android.arch.navigation:navigation-ui-ktx:$version_navigation"
}
```
#### 2. Add the navigation graph to the project
n the Project window, right-click on the res directory and select New > Android resource file. The New Resource dialog appears.

Select Navigation as the resource type, and give it the file name of navigation. Make sure it has no qualifiers. Select the navigation.xml file in the new navigation directory under res, and make sure the design tab is selected.

#### 3.Add the NavHostFragment to your activity's xml file:
```xml
<!-- The NavHostFragment within the activity_main layout -->
<fragment
   android:id="@+id/myNavHostFragment"
   android:name="androidx.navigation.fragment.NavHostFragment"
   android:layout_width="match_parent"
   android:layout_height="match_parent"
   app:navGraph="@navigation/navigation"
   app:defaultNavHost="true"
   />
```
#### 4. Adding the Title and Game Fragments to the Navigation Graph
Within the navigation editor, click the add button. A list of fragments and activities will drop down. Add fragment_title first, as it is the start destination. (you’ll see that it will automatically be set as the Start Destination for the graph.) 
![github-large](https://i.imgur.com/FrNIdvA.png)
Next, add the fragment_game.
```xml
<!-- The complete game fragment within the navigation XML, complete with tools:layout. -->
<fragment
   android:id="@+id/gameFragment"
   android:name="com.example.android.navigation.GameFragment"
   android:label="GameFragment"
   tools:layout="@layout/fragment_game" />
```
#### 5. Connecting the Title and Game Fragments with an Action
Begin by hovering over the titleFragment. You’ll see a circular connection point on the right side of the fragment view. Click on the connection point and drag it to gameFragment to add an Action that connects the two fragments.

#### 6. Navigating when the Play Button is Hit
Return to onCreateView in the TitleFragment Kotlin code. The binding class has been exposed, so you just call binding.playButton.setOnClickListener. Inside our lambda, use view.findNavcontroller to get the navigation controller for our Navigation Host Fragment. Then, use the navController to navigate using the titleFragment to gameFragment action, by calling navigate(R.id.action_titleFragment_to_gameFragment)
```kotlin
//The complete onClickListener with Navigation
binding.playButton.setOnClickListener { view: View ->
        view.findNavController().navigate(R.id.action_titleFragment_to_gameFragment)
}
```
Navigation can create the onClick listener for us. We can replace the anonymous function with the Navigation.createNavigateOnClickListener call.
```kotlin
//The complete onClickListener with Navigation using createNavigateOnClickListener
binding.playButton.setOnClickListener(
        Navigation.createNavigateOnClickListener(R.id.action_titleFragment_to_gameFragment))
```
----------------------------------------------
## Back Stack Manipulation
#### 1. For the actions connecting the gameFragment to the gameOverFragment and gameFragment to the gameWonFragment, set the pop behavior to popTo gameFragment inclusive OR popTo titleFragment without the inclusive flag.

Go to the navigation editor and select the action for navigating from the GameFragment to the GameOverFragment. 
![github-large](https://i.imgur.com/0AzDnfp.png)
Select PopTo GameFragment in the attributes pane with the inclusive flag OR PopTo TitleFragment without the inclusive flag.

#### 2. Add an onClick Handler for the tryAgain button that navigates to the gameFragment and the nextMatch button that navigates to the gameWonFragment

----------------------------------------------
## Add support for the Up Button
#### 1. Link the NavController to the ActionBar with NavigationUI.setupWithNavController.
Let's move to MainActivity. We need to find the NavController. Since we’re in the Activity now, we’ll use the alternate method of finding the controller from the ID of our NavHostFragment using the KTX extension function.
```kotlin
val navController = this.findNavController(R.id.myNavHostFragment)
```
Link the NavController to our ActionBar.
```kotlin
NavigationUI.setupActionBarWithNavController(this, navController)
```
#### 2. Override the onSupportNavigateUp method from the activity and call navigateUp in nav controller.

Finally, we need to have the Activity handle the navigateUp action from our Activity. To do this we override onSupportNavigateUp, find the nav controller, and then we call navigateUp().
```kotlin
override fun onSupportNavigateUp(): Boolean {
   val navController = this.findNavController(R.id.myNavHostFragment)
   return navController.navigateUp()
}
```
----------------------------------------------
## Add a menu
#### 1. Add AboutFragment to the navigation graph
Click the "add" button. A list of fragments and activities will drop down. Add fragment_about. Name it with the title_about_trivia string. Set its id to aboutFragment. The menu will need this id to navigate to the correct fragment.

#### 2. Create new menu resource.
Right click on the res folder within the Android project and select New Resource File. We’ll call this one overflow_menu, with resource type of Menu. 
![github-large](https://video.udacity-data.com/topher/2018/October/5bc5208f_screen-shot-2018-10-16-at-10.19.28-am/screen-shot-2018-10-16-at-10.19.28-am.png)

#### 3. Create “About” menu item with ID of aboutFragment destination
Make sure the design tab is selected. Drag a menu item from the palette into the component tree below. Move to the attributes pane. Set the new item's id to aboutFragment, its destination. That's the id you used when adding the About fragment to the navigation graph. For title, we can use @string/about. The rest of the attributes should be left as their defaults.
![github-small](https://video.udacity-data.com/topher/2018/October/5bc520b8_screen-shot-2018-10-16-at-10.20.13-am/screen-shot-2018-10-16-at-10.20.13-am.png)

#### 4. Call setHasOptionsMenu() in onCreateView of TitleFragment

Next we need to tell Android that our TitleFragment has a menu. In onCreateView call setHasOptionsMenu(true).
```kotlin
override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?,
                         savedInstanceState: Bundle?): View? {
   ...
   setHasOptionsMenu(true)
   return binding.root
}
```
#### 5. Override onCreateOptionsMenu and inflate menu resource

Next we need to override onCreateOptionsMenu and inflate our new menu resource using the provided menu inflater and menu structure.
```kotlin
override fun onCreateOptionsMenu(menu: Menu?, inflater: MenuInflater?) {
   super.onCreateOptionsMenu(menu, inflater)
   inflater?.inflate(R.menu.overflow_menu, menu)
}
```
#### 6. Override onOptionsItemSelected and call NavigationUI.onNavDestinationSelected

Finally, we need to override onOptionsItemSelected to connect it to our NavigationUI.
```kotlin
override fun onOptionsItemSelected(item: MenuItem?): Boolean {
   return NavigationUI.onNavDestinationSelected(item!!,
           view!!.findNavController())
           || super.onOptionsItemSelected(item)
}
```
