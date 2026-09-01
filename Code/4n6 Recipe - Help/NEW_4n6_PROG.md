création
``` java 
    private DatePicker datePick;

        datePick = findViewById(R.id.DatePickerTask);

        binding.BtnCreateTask.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                String TaskName = binding.UltraFreshTaskItem.getText().toString();
                int day =  datePick.getDayOfMonth();
                int month =  (datePick.getMonth() + 1);
                int year =  datePick.getYear();
                System.out.println(day + "/" + month + "/" + year);
                Date TaskDate = new Date(year,month, day);
                System.out.println(TaskDate);
                AddTaskRequest addTask = new AddTaskRequest();
                addTask.deadline = TaskDate;
                addTask.name = TaskName;
                //service.AddTask()

                Intent i = new Intent(CreationActivity.this, AccueilActivity.class);
                startActivity(i);
            }
        });

        service.AddTask(addTask).enqueue(new Callback<String>() {
                    @Override
                    public void onResponse(Call<String> call, Response<String> response) {
                        if (response.isSuccessful()) {
                            Intent i = new Intent(CreationActivity.this, AccueilActivity.class);
                            startActivity(i);
                        }else{
                            Log.i("RETROFIT", response.code()+"");
                        }
                    }

                    @Override
                    public void onFailure(Call<String> call, Throwable t) {
                        Log.i("RETROFIT", t.getMessage());
                    }
                });
```

``` java
public interface Service {

    //region Account
    //Post Sign in (Conexion)
    @POST("/api/id/signin")
    Call<SigninResponse> createPostSignIn(@Body SignupRequest signupRequest);

    //Post sing up (inscription)
    @POST("/api/id/signup")
    Call<SigninResponse> createPostSiginUp(@Body SignupRequest signupRequest);

    //Post log out (Deconexion)
    @POST("/api/id/signout")
    Call<String> createPostSignOut();
    //endregion

    //region Task
    //POST
    @POST("/api/add")
    Call<String> AddTask(@Body AddTaskRequest addTaskRequest);

    //GET
    @GET("/api/progress/{taskID}/{value}")
    Call<String> ProgressUpdate(@Path("taskID") Long l, @Path("value") int v);

    //GET
    @GET("/api/home")
    Call<List<HomeItemResponse>> GetHome();

    //GET
    @GET("/api/detail/{id}")
    Call<TaskDetailResponse> TemporaryName(@Path("id") long l);
    //endregion

}
```

accueil
``` java
service.GetHome().enqueue(new Callback<List<HomeItemResponse>>() {
            @Override
            public void onResponse(Call<List<HomeItemResponse>> call, Response<List<HomeItemResponse>> response) {
                adapter.list.addAll(response.body());
                //Check l'erreur. 
            }

            @Override
            public void onFailure(Call<List<HomeItemResponse>> call, Throwable t) {

            }
        });
```
