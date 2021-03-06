# Switch

**简单介绍**


一个简单的自定义开关,能够单击实现开关效果,并能够滑动来实现开关.下面直接贴代码.

	package com.example.bing.onoff;

	import android.content.Context;
	import android.graphics.Bitmap;
	import android.graphics.BitmapFactory;
	import android.graphics.Canvas;
	import android.graphics.Paint;
	import android.util.AttributeSet;
	import android.view.MotionEvent;
	import android.view.View;

	/**
	 * Created by bing on 2014/09/16.
	 */
	public class ToggleView extends View implements View.OnClickListener {
	    private Paint mPaint;
	    /**
	     * 做为背景图的图片
	     */
	    private Bitmap backGround;
	    /**
	     * 滑动按钮的图片
	     */
	    private Bitmap button;
	
	    /**
	     * 距离左边的最大距离
	     */
	    private int maxDistance;
	    /**
	     * 滑动按钮的左边距
	     */
	    private int LeftDistance;
	
	    /**
	     * 是否是滑动状态
	     */
	    private boolean isDrop;
	
	    /**
	     * 判断当前开关的状态
	     */
	    private boolean isOn;
	
	    public ToggleView(Context context) {
	        this(context, null);
	    }
	
	    public ToggleView(Context context, AttributeSet attrs) {
	        super(context, attrs);
	        initView();
	    }
	
	    /**
	     * 初始化操作
	     */
	    private void initView() {
	        backGround = BitmapFactory.decodeResource(getResources(), R.drawable.switch_background);
	        button = BitmapFactory.decodeResource(getResources(), R.drawable.slide_button);
	        mPaint = new Paint();
	        mPaint.setAntiAlias(true);
	        maxDistance = backGround.getWidth() - button.getWidth();
	        this.setOnClickListener(this);
	    }
	
	    /**
	     * 设置当前view的测量值大小
	     */
	    @Override
	    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
	        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
	        //设置当前view的测量值大小  与 背景图的大小，一致
	        setMeasuredDimension(backGround.getWidth(), backGround.getHeight());
	        //得到的大小与父控件的大小是一样的
	        // setMeasuredDimension(getMeasuredWidth(),getMeasuredHeight());
	    }
	
	    /**
	     * 绘制view的内容
	     */
	    @Override
	    protected void onDraw(Canvas canvas) {
	        canvas.drawBitmap(backGround, 0, 0, mPaint);
	        canvas.drawBitmap(button, LeftDistance, 0, mPaint);
	    }
	
	    /**
	     * 响应view的点击事件
	     */
	    @Override
	    public void onClick(View v) {
	        if (isDrop) {
	            return;
	        }
	        isOn = !isOn;
	        flushState();
	    }
	
	    /**
	     * 更新状态
	     */
	    private void flushState() {
	        if (isOn) {
	            LeftDistance = maxDistance;
	        } else {
	            LeftDistance = 0;
	        }
	        flushView();
	    }
	
	    /**
	     * 刷新页面
	     */
	    private void flushView() {
	        //为了防止滑动到控件的外面
	        if (LeftDistance < 0) {
	            LeftDistance = 0;
	        }
	        if (LeftDistance > maxDistance) {
	            LeftDistance = maxDistance;
	        }
	        invalidate();
	    }
	
	    private int lastX;
	
	    @Override
	    public boolean onTouchEvent(MotionEvent event) {
	        super.onTouchEvent(event);
	        int x = (int) event.getX();
	        switch (event.getAction()) {
	            case MotionEvent.ACTION_DOWN:
	                isDrop = false;
	                lastX = x;
	                break;
	            case MotionEvent.ACTION_MOVE:
	                int dx = x - lastX;
	                LeftDistance += dx;
	                if (Math.abs(dx) > 3) {
	                    isDrop = true;
	                }
	                lastX = x;
	                break;
	            case MotionEvent.ACTION_UP:
	                if (isDrop) {
	                    if (LeftDistance > maxDistance / 2) {
	                        isOn = true;
	                    } else {
	                        isOn = false;
	                    }
	                    flushState();
	                }
	                break;
	        }
	        flushView();
	        return true;
	    }
	}

