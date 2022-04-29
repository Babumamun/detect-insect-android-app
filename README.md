# detect-insect-android-app
User interfaces
Login page: 
This the login portal in to the system. The login in takes the users email and password 

    ![image](https://user-images.githubusercontent.com/62865086/165876575-c00029f6-023c-4b33-8f56-baa8d9bd0be9.png)



Figure 5.1 login page
Following part of code is the key code for the log in page: 
public class LoginActivity extends AppCompatActivity {
 
    TextInputEditText etLoginEmail;
    TextInputEditText etLoginPassword;
    TextView tvRegisterHere;
    Button btnLogin;
 
    FirebaseAuth mAuth;
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_login);
 
        etLoginEmail = findViewById(R.id.etLoginEmail);
        etLoginPassword = findViewById(R.id.etLoginPass);
        tvRegisterHere = findViewById(R.id.tvRegisterHere);
        btnLogin = findViewById(R.id.btnLogin);
 
        mAuth = FirebaseAuth.getInstance();
 
        btnLogin.setOnClickListener(view -> {
            loginUser();
        });
        tvRegisterHere.setOnClickListener(view ->{
            startActivity(new Intent(LoginActivity.this, RegisterActivity.class));
        });
    }
 
    private void loginUser(){
        String email = etLoginEmail.getText().toString();
        String password = etLoginPassword.getText().toString();
 
        if (TextUtils.isEmpty(email)){
            etLoginEmail.setError("Email cannot be empty");
            etLoginEmail.requestFocus();
        }else if (TextUtils.isEmpty(password)){
            etLoginPassword.setError("Password cannot be empty");
            etLoginPassword.requestFocus();
        }else{
            mAuth.signInWithEmailAndPassword(email,password).addOnCompleteListener(new OnCompleteListener<AuthResult>() {
                @Override
                public void onComplete(@NonNull Task<AuthResult> task) {
                    if (task.isSuccessful()){
                        Toast.makeText(LoginActivity.this, "User logged in successfully", Toast.LENGTH_SHORT).show();
                        startActivity(new Intent(LoginActivity.this, MainActivity.class));
                    }else{
                        Toast.makeText(LoginActivity.this, "Log in Error: " + task.getException().getMessage(), Toast.LENGTH_SHORT).show();
                    }
                }
            });
        }
    }
 
}


Register page: This the registration portal in to the system. The registration in takes the users email and redirects to a complete signup page to provide more information about the user.

![image](https://user-images.githubusercontent.com/62865086/165876769-1c29c8d3-7529-44b8-9691-9326085a04ab.png)


Figure 5.2 Register page
The following code below is for the design of the register page.

public class RegisterActivity extends AppCompatActivity {
 
    TextInputEditText etRegEmail;
    TextInputEditText etRegPassword;
    TextView tvLoginHere;
    Button btnRegister;
 
    FirebaseAuth mAuth;
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_sign_up);
 
        etRegEmail = findViewById(R.id.etRegEmail);
        etRegPassword = findViewById(R.id.etRegPass);
        tvLoginHere = findViewById(R.id.tvLoginHere);
        btnRegister = findViewById(R.id.btnRegister);
 
        mAuth = FirebaseAuth.getInstance();
 
        btnRegister.setOnClickListener(view ->{
            createUser();
        });
 
        tvLoginHere.setOnClickListener(view ->{
            startActivity(new Intent(RegisterActivity.this, LoginActivity.class));
        });
    }
 
    private void createUser(){
        String email = etRegEmail.getText().toString();
        String password = etRegPassword.getText().toString();
 
        if (TextUtils.isEmpty(email)){
            etRegEmail.setError("Email cannot be empty");
            etRegEmail.requestFocus();
        }else if (TextUtils.isEmpty(password)){
            etRegPassword.setError("Password cannot be empty");
            etRegPassword.requestFocus();
        }else{
            mAuth.createUserWithEmailAndPassword(email,password).addOnCompleteListener(new OnCompleteListener<AuthResult>() {
                @Override
                public void onComplete(@NonNull Task<AuthResult> task) {
                    if (task.isSuccessful()){
                        Toast.makeText(RegisterActivity.this, "User registered successfully", Toast.LENGTH_SHORT).show();
                        startActivity(new Intent(RegisterActivity.this, LoginActivity.class));
                    }else{
                        Toast.makeText(RegisterActivity.this, "Registration Error: " + task.getException().getMessage(), Toast.LENGTH_SHORT).show();
                    }
                }
            });
        }
    }
 
}


Homepage: The homepage show cases the option to take the picture or choose from the gallery.

![image](https://user-images.githubusercontent.com/62865086/165876793-4a70d01f-7aa1-49cb-8692-2f9f24fa7ebe.png)



Figure 5.4 homepage


    
Take image : This page indicates opining camera to take the picture.



![image](https://user-images.githubusercontent.com/62865086/165876808-33aa3ffb-967f-4b45-be07-6a272831df50.png)

                              Figure 5.5 opining camera
The following code below illustrates when user click the take picture button then how does it work.
camera.setOnClickListener(new View.OnClickListener() {
    @RequiresApi(api = Build.VERSION_CODES.M)
    @Override
    public void onClick(View view) {
        if (checkSelfPermission(Manifest.permission.CAMERA) == PackageManager.PERMISSION_GRANTED) {
            Intent cameraIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
            startActivityForResult(cameraIntent, 3);
        } else {
            requestPermissions(new String[]{Manifest.permission.CAMERA}, 100);
        }
    }
});

Choose image from the gallery: User can choose image from the gallery.Just simply clicking by launch the gallery.

                       

Figure 5.6 opining gallery

The below code is for open the gallery.
gallery.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View view) {
            Intent cameraIntent = new Intent(Intent.ACTION_PICK, MediaStore.Images.Media.EXTERNAL_CONTENT_URI);
            startActivityForResult(cameraIntent, 1);
        }
    });
}

Detecting image page: this page works is, when a user selected one image then this automatically start to detected the image. This image compare to the our train model and then it give the result. 
         

![image](https://user-images.githubusercontent.com/62865086/165876819-a833d4ec-0926-4d49-b346-571161ee4f34.png)

Figure 5.7 detecting insect
The code below is of a query that requests to compare this image to our train model image.
public void classifyImage(Bitmap image){
    try {
        ModelUnquant model = ModelUnquant.newInstance(getApplicationContext());
        ByteBuffer byteBuffer = ByteBuffer.allocateDirect(4 * imageSize * imageSize * 3);
        byteBuffer.order(ByteOrder.nativeOrder());
        // Creates inputs for reference.
        TensorBuffer inputFeature0 = TensorBuffer.createFixedSize(new int[]{1, 224, 224, 3}, DataType.FLOAT32);
        int[] intValues = new int[imageSize * imageSize];
        image.getPixels(intValues, 0, image.getWidth(), 0, 0, image.getWidth(), image.getHeight());
        int pixel = 0;
        //iterate over each pixel and extract R, G, and B values. Add those values individually to the byte buffer.
        for(int i = 0; i < imageSize; i ++){
            for(int j = 0; j < imageSize; j++){
                int val = intValues[pixel++]; // RGB
                byteBuffer.putFloat(((val >> 16) & 0xFF) * (1.f / 1));
                byteBuffer.putFloat(((val >> 8) & 0xFF) * (1.f / 1));
                byteBuffer.putFloat((val & 0xFF) * (1.f / 1));
            }
        }
        inputFeature0.loadBuffer(byteBuffer);

        // Runs model inference and gets result.
        ModelUnquant.Outputs outputs = model.process(inputFeature0);
        TensorBuffer outputFeature0 = outputs.getOutputFeature0AsTensorBuffer();
        float[] confidences = outputFeature0.getFloatArray();
        // find the index of the class with the biggest confidence.
        int maxPos = 0;
        float maxConfidence = 0;
        for (int i = 0; i < confidences.length; i++) {
            if (confidences[i] > maxConfidence) {
                maxConfidence = confidences[i];
                maxPos = i;
            }
        }
        String[] classes = {"rice bug", "brown planthopper", "Scirpophaga incertulas"};
        result.setText(classes[maxPos]);
        // Releases model resources if no longer used.
        model.close();
    } catch (IOException e) {
        // TODO Handle the exception
    }
}


Get result page: this page works is, after detecting image user will be able to see the result of what kind of insect effect on his crops field.

![image](https://user-images.githubusercontent.com/62865086/165876829-fafdd63c-b0f1-41bc-9a65-412a14856578.png)

![image](https://user-images.githubusercontent.com/62865086/165876879-981ff5d1-c9d2-4f5c-9ab2-4912a723762b.png)


Figure 5.8 result page
Get result page: This page source code is shown below.

@Override
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
        if(resultCode == RESULT_OK){
            if(requestCode == 3){
                Bitmap image = (Bitmap) data.getExtras().get("data");
                int dimension = Math.min(image.getWidth(), image.getHeight());
                image = ThumbnailUtils.extractThumbnail(image, dimension, dimension);
                imageView.setImageBitmap(image);

                image = Bitmap.createScaledBitmap(image, imageSize, imageSize, false);
                classifyImage(image);
            }else{
                Uri dat = data.getData();
                Bitmap image = null;
                try {
                    image = MediaStore.Images.Media.getBitmap(this.getContentResolver(), dat);
                } catch (IOException e) {
                    e.printStackTrace();
                }
                imageView.setImageBitmap(image);

                image = Bitmap.createScaledBitmap(image, imageSize, imageSize, false);
                classifyImage(image);
            }
        }
        super.onActivityResult(requestCode, resultCode, data);
    }

}


![image](https://user-images.githubusercontent.com/62865086/165876915-79456545-eb10-410a-95fc-3c5eb61d0644.png)



