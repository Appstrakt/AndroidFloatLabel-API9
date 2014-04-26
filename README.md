AndroidFloatLabel Fork with API9+ compatibility
=================

This repository contains an Android library project for Android 2.3+ with a custom view that implements the Float Label pattern (http://dribbble.com/shots/1254439--GIF-Float-Label-Form-Interaction) and an example project using the Float Label library.  The custom View, FloatLabel, basically consists of two pieces: the EditText and the label (a TextView).

YouTube demo: http://www.youtube.com/watch?v=9VoVxw8aAx0

For most use, you can simply use the custom view in your XML layout, specifying a label to use as both the EditText hint and the label TextView with the <code>android:hint</code> property.  Example:

    <com.iangclifton.android.floatlabel.FloatLabel
        android:id="@+id/float_label_1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="@string/example_label" />

You can also dynamically set the label with <code>floatLabel.setLabel("Custom Label")</code> or <code>floatLabel.setLabel(R.string.custom_label)</code>.

Custom Layout
-------------

If you want to specify a custom layout to use, you can do something like this:

    <com.iangclifton.android.floatlabel.FloatLabel
        android:id="@+id/float_label_custom_layout_1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="@string/example_label"
        android:layout="@layout/custom_float_label" />

Your custom layout should include a label TextView (id/float_label) and an EditText (id/edit_text).  Right now, the custom layouts are extremely limited because the FloatLabel simply lays out the label and the EditText and ignores all other views.  This is very efficient but also prevents you from creating a much more complex layout.  Here's an example:

    <?xml version="1.0" encoding="utf-8"?>
    <merge xmlns:android="http://schemas.android.com/apk/res/android" >
        <TextView
            android:id="@id/float_label"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:lines="1"
            android:textAppearance="?android:attr/textAppearanceSmall" />
        <EditText
            android:id="@id/edit_text"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:inputType="text|textAutoCorrect|textCapSentences|textAutoComplete" />
    </merge>

Custom Animation for API 9+
----------------

You can override the animations used to show and hide the label by implementing a <code>FloatLabel.LabelAnimator</code> and calling <code>floatLabel.setLabelAnimator(new MyLabelAnimator());</code>.  Note that you should use the alpha property of the label to show and hide it rather than the View.setVisibility(int) method.  Example:

    private static class CustomLabelAnimator implements FloatLabel.LabelAnimator {
        /*package*/ static final float SCALE_X_SHOWN = 1f;
        /*package*/ static final float SCALE_X_HIDDEN = 2f;
        /*package*/ static final float SCALE_Y_SHOWN = 1f;
        /*package*/ static final float SCALE_Y_HIDDEN = 0f;

        @Override
        public void onDisplayLabel(View label) {
            final float shift = label.getWidth() / 2;
            ViewHelper.setScaleX(label,SCALE_X_HIDDEN);
            ViewHelper.setScaleY(label,SCALE_Y_HIDDEN);
            ViewHelper.setX(label, shift);
            ViewPropertyAnimator.animate(label).alpha(1).scaleX(SCALE_X_SHOWN).scaleY(SCALE_Y_SHOWN).x(0f);
        }

        @Override
        public void onHideLabel(View label) {
            final float shift = label.getWidth() / 2;
            ViewHelper.setScaleX(label,SCALE_X_SHOWN);
            ViewHelper.setScaleY(label,SCALE_Y_SHOWN);
            ViewHelper.setX(label,0f);
            ViewPropertyAnimator.animate(label).alpha(0).scaleX(SCALE_X_HIDDEN).scaleY(SCALE_Y_HIDDEN).x(shift);
        }
    }
