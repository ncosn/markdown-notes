```java
// Fresco初始化,需要在setContentView之前初始化
Fresco.initialize(this);


Uri uri = Uri.parse(businessNoteUrl);
//根据请求路径生成ImageRequest的构造者
ImageRequestBuilder builder = ImageRequestBuilder.newBuilderWithSource(uri);
ImageRequest imageRequest = builder.build();

DraweeController controller = Fresco.newDraweeControllerBuilder()
    .setImageRequest(imageRequest)
    .setOldController(dvBusinessNote.getController())
    .setControllerListener(new ControllerListener<ImageInfo>() {
        @Override
        public void onSubmit(String s, Object o) {}
        
        /**
         * 获取到图片后，触发加载完成的回调方法(onFinalImageSet)，从图片信息对象(ImageInfo)中知道其宽高，再重新设置SimpleDraweeView的真实宽高
         *
         * @param s
         * @param imageInfo
         * @param animatable
         */
         @Override
         public void onFinalImageSet(String s, @Nullable ImageInfo imageInfo, @Nullable Animatable animatable) {
             LinearLayout.LayoutParams params = (LinearLayout.LayoutParams) dvBusinessNote.getLayoutParams();
             params.width = screenWidth;
             params.height = (int) ((float)imageInfo.getHeight()/ imageInfo.getWidth() * screenWidth);
             dvBusinessNote.setLayoutParams(params);
         }

         @Override
         public void onIntermediateImageSet(String s, @Nullable ImageInfo imageInfo) {}

         @Override
         public void onIntermediateImageFailed(String s, Throwable throwable) {}

         @Override
         public void onFailure(String s, Throwable throwable) {}
        
         @Override
         public void onRelease(String s) {}
    })
    // other setters as you need
    .build();
    dvBusinessNote.setController(controller);
```