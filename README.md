# Camera-used-surfaceview
Hello.
This is Skuld Chen, He is a new guy for Android developer since on 20150302. 
His First project is camera but customer. Therefore, any about Mediastore, camera2, and so on does not used.
Also due to used surface, when this program would saving photo, it should be used different ways.
And when saving, many sample program only provide small way, whcih is 200*200 or less.
He soluted those service. 
He would not provide very well because his mother language is T-Chinese, so he provide some sample code
want reader can reading by themself. 
This sample code used SurfaceView, therefore Activity life does not match in this program.
This sample code also required callback function.
Reader could require basic element before they start this program.
Because author have algorithm research BG skill, the algorithm will wase many time, so he recommand subprogram into one .java file. 
This means that DO NOT create many subprogram in many .java files. 
This program should be one .java file.


----------------------------------------------------------------------------------------------------------------------------

	private void skuldonCreat()
	{
		this.setRequestedOrientation(1);// 定向
		// ------------------------------------------------------------------------------------------------------------------------
		//skuldpannelDef();
        //skuldradioDef();                
        //skuldcamera();        
        skuldseekbar();
        sk_seekbtncount();
        skuldcamera(); 
	}
	
	    //-------------------------------------------------------------------Camera preview-------------------------------------------------------------------
    //
    //
    //-------------------------------------------------------------------Camera preview-------------------------------------------------------------------
    Camera sk_mCamera;
    SurfaceView sk_surfaceview; // aka cameraView;
	private void skuldcamera()
	{
		sk_surfaceview = (SurfaceView)findViewById(R.id.surfaceView1);
		sk_surfaceview.getHolder().setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);
		sk_surfaceview.getHolder().addCallback(this);
	    //Log.d("CAMERA","actCamera on Create");
    
	}
	@Override
	public void surfaceCreated(SurfaceHolder holder)
	{ 		    		
		try
		{
			sk_mCamera = Camera.open();
		    //mCamera.setPreviewDisplay(sk_surfaceview.getHolder());// ok can work			
		    //this.mCamera.setPreviewDisplay(sk_surfaceview.getHolder());
			sk_mCamera.setPreviewDisplay(holder);
			
		}
    	catch (Exception e)
    	{
    	    e.printStackTrace();
    	}	    	
	}
    @Override
    public void surfaceChanged(SurfaceHolder holder, int format, int width, int height)
    {
//	        Camera.Parameters parameters = mCamera.getParameters();
//	        List<Camera.Size> sizes = parameters.getSupportedPreviewSizes();
//	        Camera.Size selected = sizes.get(0);
//	        parameters.setPreviewSize(selected.width,selected.height);
//	        mCamera.setParameters(parameters);

	        //mCamera.setDisplayOrientation(90); 
    	sk_mCamera.setDisplayOrientation(getPreviewDegree(MainActivity.this));	        
    	sk_mCamera.startPreview();
	    
	}
    public static int getPreviewDegree(Activity activity)
    {
    	int rotation = activity.getWindowManager().getDefaultDisplay().getRotation();
    	int degree=0;
    	switch(rotation)
    	{
    	case Surface.ROTATION_0:
    		degree = 90;
    		break;
    	case Surface.ROTATION_90:
    		degree= 0;
    		break;
	    case Surface.ROTATION_180:
	        degree=270;
	    	break;
	    case Surface.ROTATION_270:
	        degree = 180;
	        break;
	    }
	    return degree;
	    
	}
	
    @Override
	public void surfaceDestroyed(SurfaceHolder holder) 
	{
    	if (sk_mCamera != null)    		
    	{
    		closeCam();
    	}
    	Log.i("PREVIEW","surfaceDestroyed");
    }
	    
	private void closeCam()
	{	
	    //finish();
		sk_mCamera.setPreviewCallback(null);
		sk_mCamera.stopPreview();
		sk_mCamera.release();
		sk_mCamera = null;	       
	    Log.d("closeCamera","closeCam()");
	}
       
    //**************************************************************End of Camera preview**************************************************************
	//
    //
    //**************************************************************End of Camera preview**************************************************************
 
    //-------------------------------------------------------------------Camera shooting and saving-------------------------------------------------------------------
    //
    //
    //-------------------------------------------------------------------Camera shooting and saving-------------------------------------------------------------------
	//TODO
		
	private void skuldsetforcamera()
	{
    	Camera.Parameters parameters = sk_mCamera.getParameters();
    	parameters.setPictureSize(2560, 1920);
    	parameters.set("rotation", 90);
    	sk_mCamera.setParameters(parameters);
	}	
	
    public void btnTakeshooting1(View v) 
	{
		// do something, take shooting and auto to save photo.
    	skuldsetforcamera();

    	
    	// ***********************************
	    //	    	
    	sk_mCamera.takePicture( this, null, mytake);  // callBack
    	Log.d("onClick","btnTakeshooting1");
					

    	
	}
	@Override
	public void onShutter() {
	    Toast.makeText(this, "Click!", Toast.LENGTH_SHORT).show();
	}
	   

	
	@Override
	public void onPictureTaken(byte[] data, Camera camera) 
	{

	}

    
    PictureCallback mytake = new PictureCallback() 
    {
        @Override
        public void onPictureTaken(byte[] data, Camera camera) 
        {
        	File mediaStorageDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES);	        	
        	String timeStamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());	  
        	/// please change below of IMG_xxx_ when change project for debug easily
        	File imageFile = new File(mediaStorageDir, "IMG_Liq_"+ timeStamp + ".jpg" );

            try
            {
               	// --------------------------------------------------------------------------------------------

            	
            	//FileOutputStream fos = new FileOutputStream(pictureFile);
            	FileOutputStream fos = new FileOutputStream(imageFile);
	            fos.write(data);
	            fos.flush();
	            fos.close();
								// --------------------------------------------------------------------------------------------
				// 2015 0520
				// After the photo has been saved, add this line to let the Photos app know there are new photos:
				sendBroadcast(new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE, Uri.parse("file://" + imageFile.getAbsolutePath())));

                // --------------------------------------------------------------------------------------------
                // Do NOT mark This before finished 
            	Log.i("PictureCallback","image exist, and finished save");
 

            }
            catch(Exception ex)
            {
                Toast.makeText(Activity_Main.this, "Save file error !",Toast.LENGTH_LONG).show();
            }
            camera.startPreview();
            //showBtn.setEnabled(true);
        }
    };
	
    //**************************************************************End of Camera shooting and saving**************************************************************
	//
    //
    //**************************************************************End of Camera shooting and saving**************************************************************
