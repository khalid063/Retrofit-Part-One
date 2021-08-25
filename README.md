# Retrofit-Part-One Code Step
Api link : https://jsonplaceholder.typicode.com/
	   https://jsonplaceholder.typicode.com/users

1. dependenci Add in gradle

    // Retrofit implements
    implementation 'com.squareup.retrofit2:retrofit:2.1.0'
    implementation 'com.squareup.retrofit2:converter-gson:2.1.0'

2. get internet permission
   <uses-permission android:name="android.permission.INTERNET" />  // into Manifast file

   android:usesCleartextTraffic="true"    // Manifast file inside of application


3. Go to java file and create -> Post.java class with getter method .(as a model class for putting data after receiveing)

   public class Post {
    private int userId;
    private int id;
    private String title;

    @SerializedName("body")
    private String text;

    public int getUserId() {
        return userId;
    }

    public int getId() {
        return id;
    }

    public String getTitle() {
        return title;
    }

    public String getText() {
        return text;
    }
}



4. go to mainActivity.xml take scrollView with a text for Data showing

<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="8dp"			// padding for design
    tools:context=".MainActivity">

    <ScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Retrofit"
            android:textColor="#000"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toTopOf="parent" />
    </ScrollView>

</androidx.constraintlayout.widget.ConstraintLayout>


5. take Interface named as : JsonPlaceHolderApi

public interface JsonPlaceHolderApi {

    @GET("posts")  // End point is posts
    Call<List<Post>> getPost();
}

*** hare 1. End point of url is "posts" and GET is the receiving methods
	 2. Call is a class of Retrofit which is use auto call of server
	 3. List is used because from Post class we will get a list of post, for containing this we need a List



6. go to MainActivity.java

	//Outside of OnCreate
	private TextView textViewResult;

	
	// inside of OnCreate
	textViewResult = findViewById(R.id.text_view_result);

        Retrofit retrofit = new Retrofit.Builder ()
                .baseUrl ("http://jsonplaceholder.typicode.com")
                .addConverterFactory (GsonConverterFactory.create ())
                .build ();

        JsonPlaceHolderApi jsonPlaceHolderApi = retrofit.create (JsonPlaceHolderApi.class);

        Call<List<Post>> call = jsonPlaceHolderApi.getPosts();


        call.enqueue (new Callback<List<Post>> () {
            @Override
            public void onResponse(Call<List<Post>> call, Response<List<Post>> response) {

                Log.d("msg_one","Response : " + response.body().get(0).getId());

                if (!response.isSuccessful ()){
                    textViewResult.setText ("Code : "+ response.code ());
                    return;
                }

                List<Post> posts = response.body ();
                for (Post post : posts){
                    String content = "";
                    content += "ID : "+ post.getId () + "\n";
                    content += "User Id : "+ post.getUserId () + "\n";
                    content += "Title : "+ post.getTitle () + "\n";
                    content += "Text : "+ post.getText () + "\n\n";

                    textViewResult.append (content);
                }

            }

            @Override
            public void onFailure(Call<List<Post>> call, Throwable t) {

                textViewResult.setText (t.getMessage ());

            }
        });
