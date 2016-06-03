---
layout: post
category : lessons
tagline: "Hello Firebase"
tags : [firebase, android, java, tutorial, todo app]
---

> This tutorial assumes you are familiar with basics of Android development including Android Studio, java and gradle.

# What is Firebase?

![Firebase logo](https://1.bp.blogspot.com/-YIfQT6q8ZM4/Vzyq5z1B8HI/AAAAAAAAAAc/UmWSSMLKtKgtH7CACElUp12zXkrPK5UoACLcB/s1600/image00.png)

**Firebase** is a multi purpose sdk which can be used to integrate push notifications in your app, app analytics and a whole lot of other features which you can review on its site.

We are going to use **Firebase Realtime Database** here to develop a simple To-do app.

## Step 1. Setting up Android Project

> Prerequisite : You need to have Studio with version **1.5** or higher for the services to work.

Create a new Android Studio Project and name it TodoApp.
Now note down the **package name**, we will need it to make the `google-services.json` file.

Head over to [Firebase console](https://console.firebase.google.com/) and click **Create New Project**. Enter TodoApp as the name project and click create project. You will be redirected to a screen like this

![Firebase Console](http://i.imgur.com/41Rq8Ij.jpg)

Click Add firebase to your Android project and in the popup - **Enter your package name**

![Package popup](http://i.imgur.com/s7EuCON.jpg)

Press Add app and now you will get the **google-services.json** downloaded automatically. Now keep pressing continue to finish the integration steps and get back to the project.


Drag the json you downloaded into your project root (where .build gradle file is of the app module). The json file actually includes all the relevant settings for the firebase app including the firebase database url for your app.

### Setup Gradle

Open your project build.gradle file and add the following dependencies 

{% highlight xml %}

 dependencies {
      classpath 'com.android.tools.build:gradle:2.1.0'
      classpath 'com.google.gms:google-services:3.0.0'
  }
{% endhighlight %}

Now open your app module build.grade and add these dependencies
{% highlight xml %}
    compile 'com.google.firebase:firebase-core:9.0.1'
    compile 'com.google.firebase:firebase-database:9.0.1'
{% endhighlight %}

At the end of the file we need to apply our google play servcies plugin :

`apply plugin: 'com.google.gms.google-services'`

Sync your gradle and you will have the firebase setup in your project.

## Step 2. Making the App UI

We need to setup a basic UI to actually see our firebase code in work. I am going to explain in short here with enough details to actually carry out yourself.

 We will be having two activity in this project. MainActivity and TodoActivity.  
 The UI you need to make is shown in the screenshot
 
 ![App's UI](http://i.imgur.com/iyLXjKU.jpg)
 
 The first activity has a recycleview and a Floating action button (FAB) in it. We will fill the recycle view with our firebase todo list and the action button takes to the TodoActivity using intent.
 
 
 The TodoActivity has three Textviews and two EditText + a date picker in it. Pretty simple stuff.
 Make a new **TodoActivity.java** class in your project and make the appropriate layout for it. Make sure to give ids to EditText, DatePicker to access them in activity.
 Also add a FAB in this class on which we will actually save our Todo to the firebase db. Give appt id to it
 
## Step 3. Making the Model

Our Todo won't be a ArrayList or Hashmap lying around here and there in a project. We will make a proper model class for it.
Make a new **Todo.java** in your project and replace its content with the following

{% highlight java %}

ublic class Todo implements Serializable {

    private String name;
    private String message;
    private String date;

    public Todo() {

    }
    public String getDate() {
        return date;
    }

    public void setDate(String date) {
        this.date = date;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }


    public HashMap<String,String> toFirebaseObject() {
        HashMap<String,String> todo =  new HashMap<String,String>();
        todo.put("name", name);
        todo.put("message", message);
        todo.put("date", date);

        return todo;
    }

}


{% endhighlight %}

## Step 4. Back to understanding how Firebase works

The database your app has on the firebase cloud is available on a public url like https://appname-randomIdentifer.firebaseio.com/.
Since it is public anyone can read/write to it if the url is known. To solve this the **database rules** section is provided where you can change the access/write rights.

By default firebase database require user to be authenticated before writing to the database.

### Changing Database Rules

In this begginer tutorial we are going to remove the authentication required to read/write from our database. Note: **You should not have this for a production app**.

Go to the database section in the firebase console and change to the rules tab. Update the rules with the below and click publish.

{% highlight json %}
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
{% endhighlight %}
![Rules](http://i.imgur.com/W4a6Xjm.jpg)

## Database Structure

Firebase database is actually a NoSQL
one, you won't see any tables and where clauses in it and it is therefore **a big change** for many developers out there.

Our app has a simple Todo structure which arranged in a typical SQL database would look like.


Name     | Message    | Date
First    | Message    | 10/10/16 10:10

But in **Firebase JSON syntax**, we would have to do something like this
![Db structure](http://i.imgur.com/rGOklwM.jpg)

Each todo is uniquely identified by its key which firebase creates and our todoList is actually and array for the key **todo** in our db.


___

## Coding Actual Firebase Logic

### Step 5. Saving a new Todo in the Firebase Db



Now switch to TodoActivity.java and get the reference of the FAB button and set a clicklistener to it. In it we call our **saveTodo** method.

{% highlight java %}
        FloatingActionButton fab = (FloatingActionButton) findViewById(R.id.fab);
        fab.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                saveTodo();
            }
        });
        
        void saveTodo() {
            // first section
            // get the data to save in our firebase db
            EditText nameEdtText = (EditText)findViewById(R.id.nameEditText);
            EditText messageEditText = (EditText)findViewById(R.id.messageEditText);
            DatePicker datePicker = (DatePicker) findViewById(R.id.datePicker);
            Date date = new Date();
            date.setMonth(datePicker.getMonth());
            date.setYear(datePicker.getYear());
            date.setDate(datePicker.getDayOfMonth());
    
            SimpleDateFormat format = new SimpleDateFormat("dd/MM/yyyy HH:mm");
    
            String dateString = format.format(date);
            //make the modal object and convert it into hasmap
    
          
            //second section
            //save it to the firebase db
            FirebaseDatabase database = FirebaseDatabase.getInstance();
            String key = database.getReference("todoList").push().getKey();
    
            Todo todo = new Todo();
            todo.setName(nameEdtText.getText().toString());
            todo.setMessage(messageEditText.getText().toString());
            todo.setDate(dateString);
    
            Map<String, Object> childUpdates = new HashMap<>();
            childUpdates.put( key, todo.toFirebaseObject());
            database.getReference("todoList").updateChildren(childUpdates, new DatabaseReference.CompletionListener() {
                @Override
                public void onComplete(DatabaseError databaseError, DatabaseReference databaseReference) {
                    if (databaseError == null) {
                        finish();
                    }
                }
            });
        }
{% endhighlight %}

Our code first section does the typical model object filling by getting the values from our view object. You can go ahead and add validation logic here if you want. The real firebase work is done in the second section.


In Second section , we get the database reference first and then try to get the "todoList" child in our db by calling 'database.getReference("todoList")`.
Since writing to our todoList endpoint each time will just overwrite previous values, we need to get a unique key for our new todo so that we can push it for that particular key only. We call the `push().getKey()` on our todoList endpoint.


Now we need to convert our model into a Hashmap since Firebase cannot save custom classes. **String/ArrayList/Integer and Hashmap are the only supported types**.
On the next line we define the childUpdate model that is for /todoList/key end point => set our dictionary object.
Calling the updateChildren performs the actual operation and we can pass a completion listener to detect any error that might have occured in the request.

Try running the app now and click the FAB button in MainActivity -> goes to TodoActivity and in it save a new Todo. Firebase console should start showing up data that we save in the app.

### Step 6. Retreiving all our Todo from the DB

Firebase allow you to constantly listen to a end point for any value but in our app we only want to load data once.
MainActivity code is given below :
{% highlight java %}

   public class MainActivity extends AppCompatActivity {

    RecycleAdapter adapter;
    ArrayList<Todo> todoList;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
        setSupportActionBar(toolbar);

        FloatingActionButton fab = (FloatingActionButton) findViewById(R.id.fab);
        fab.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Intent newIntent = new Intent(MainActivity.this,TodoActivity.class);
                MainActivity.this.startActivity(newIntent);
            }
        });

        todoList = new ArrayList<>();

        RecyclerView recyclerView = (RecyclerView)findViewById(R.id.myrecycleView);
        LinearLayoutManager llm = new LinearLayoutManager(this);
        recyclerView.setLayoutManager(llm);
        adapter = new RecycleAdapter();
        recyclerView.setAdapter(adapter);

        adapter.notifyDataSetChanged();
    }


    @Override
    protected void onResume() {
        super.onResume();
        FirebaseDatabase database = FirebaseDatabase.getInstance();

        database.getReference("todoList").addListenerForSingleValueEvent(
                new ValueEventListener() {
                    @Override
                    public void onDataChange(DataSnapshot dataSnapshot) {
                        todoList.clear();

                        Log.w("TodoApp", "getUser:onCancelled " + dataSnapshot.toString());
                        Log.w("TodoApp", "count = " + String.valueOf(dataSnapshot.getChildrenCount()) + " values " + dataSnapshot.getKey());
                        for (DataSnapshot data : dataSnapshot.getChildren()) {
                            Todo todo = data.getValue(Todo.class);
                            todoList.add(todo);
                        }

                        adapter.notifyDataSetChanged();
                    }

                    @Override
                    public void onCancelled(DatabaseError databaseError) {
                        Log.w("TodoApp", "getUser:onCancelled", databaseError.toException());
                    }
                });
    }

    private class RecycleAdapter extends RecyclerView.Adapter {


        @Override
        public int getItemCount() {
            return todoList.size();
        }

        @Override
        public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
            View v = LayoutInflater.from(parent.getContext()).inflate(R.layout.todo_item, parent, false);
            SimpleItemViewHolder pvh = new SimpleItemViewHolder(v);
            return pvh;
        }

        @Override
        public void onBindViewHolder(RecyclerView.ViewHolder holder, int position) {
            SimpleItemViewHolder viewHolder = (SimpleItemViewHolder) holder;
            viewHolder.position = position;
            Todo todo = todoList.get(position);
            ((SimpleItemViewHolder) holder).title.setText(todo.getName());
        }

        public final  class SimpleItemViewHolder extends RecyclerView.ViewHolder implements View.OnClickListener {
            TextView title;
            public int position;
            public SimpleItemViewHolder(View itemView) {
                super(itemView);
                itemView.setOnClickListener(this);
                title = (TextView) itemView.findViewById(R.id.myTextView);
            }

            @Override
            public void onClick(View view) {

            }
        }
    }

}


{% endhighlight %}

First we declare a ArrayList which will hold all our Todo model objects. Next we call the loadData method in our `onResume` method so that our data is refreshed everytime MainActivity is launched as well as when we come back to the view after saving a new Todo.
In **loadData** we get the reference to our **todoList** endpoint and call the **addListenerForSingleValueEvent** method since that observes the value event only one and is not kept in memory all the time listening for any real time changes in our end point. In the closure **dataSnapshot.value** contains the value of that endpoint.

If you observe the db in the console; each todo is uniquely identified by a key therefore in our case the snapshot.value actually contains children with as much number of Todo. If our model is a **Java Pojo** we can cast it into a `Todo` object using  **data.getValue(Todo.class);**. snapshot will contain all our todo so we enumberate its children which containe name,message and date of our Todo, we fill up our model with that and add it to the array. In the end we call our recycleview adapter **notifyDataSetChanged** to show the list of Todo in our UI.


### Step 7. Viewing existing Todo

We do have our todo being retrieved and the name being shown in recycleview but clicking one should go to the **TodoActivity** and show all details right ? So in the *onClick* method in your ViewHolder class

{% highlight swift %}

 @Override
public void onClick(View view) {
    Intent newIntent = new Intent(MainActivity.this,TodoActivity.class);
    newIntent.putExtra("todo", todoList.get(position));
    MainActivity.this.startActivity(newIntent);
}

{% endhighlight %}
Real simple code right there. Now you need to show those details in the outlets in your TodoActivity class. Update its **onCreate** with the following code

{% highlight swift %}
 // previous onCreate code
 if (getIntent().getExtras() != null) {
      Bundle extras = getIntent().getExtras();
      Todo todo = (Todo)extras.get("todo");
      if (todo != null) {
          EditText nameEdtText = (EditText)findViewById(R.id.nameEditText);
          EditText messageEditText = (EditText)findViewById(R.id.messageEditText);
          DatePicker datePicker = (DatePicker) findViewById(R.id.datePicker);

          nameEdtText.setText(todo.getName());
          messageEditText.setText(todo.getMessage());

          SimpleDateFormat format = new SimpleDateFormat("dd/MM/yyyy HH:mm");
          try {
              Date date = format.parse(todo.getDate());
              datePicker.updateDate(date.getYear(), date.getMonth(), date.getDate());
          } catch (ParseException e) {
              e.printStackTrace();
          }
      }
 }
 
{% endhighlight %}

That's it to have our todo app functioning on our firebase database :).

## Conclusion

**Thank you** for reading this far. For any questions you are welcome to ask in our chat room.

## Next Steps

Please take a look at our other [tutorials]({{ BASE_PATH }}../../tutorials) :)