---
layout: post
title: "Mengenal Activity Pada Android Studio"
date: 2019-03-7
categories: mobiledev
---

![]()
### Introduction
Activity adalah komponen yang dapat dilihat oleh pengguna, sehingga mereka dapat berinteraksi dengan aplikasi.
Bila dibandingkan dengan aplikasi web dan desktop, Activity sama seperti halaman dan form.

Activity tidak hidup abadi selamanya, dia bisa diciptakan dan dihancurkan.

Activity memiliki siklus hidup (lifecycle) yang merupakan kondisi yang akan dialami saat diciptakan sampai dihancurkan.

Ada beberapa kondisi yang akan dialami oleh Activity seperti yang tergambar pada flow chart berikut ini.

![]()

Penjelasannya:

- onCreate() adalah kondisi awal saat Activity baru diciptakan, biasanya dilakukan inisialisasi pada tahapan ini.
- onStart() adalah saat Activity dimulai
- onResume() adalah saat Activity dibuka kembali, biasanya dieksekusi setelah onPause()
- onPause() akan dipanggil saat ada Activity lain yang terbuka.
- onStop() adalah kondisi saat Activity tidak ditampilkan dilayar (biasanya saat pengguna menekan tombol Home).
- onRestart() adalah kondisi saat Activity kembali dibuka oleh pengguna.
- onDestroy() adalah kondisi saat Activity dihancurkan pada memori.

### Membuat Proyek Baru
Silahkan membuat project baru dengan nama <b>Tugas Activity</b>
![]()

Kemudian pilih jenis Activity yang akan dibuat,kita pilih Empty Activity.
![]()

Setelah itu isikan nama activitynya, saya akan isi dengan MainActivity.
![]()

Tunggu beberapa saat, project kita sedang dibuat.

### Saatnya Coding
Ada dua tombol yang akan kita buat:
1. Tombol untuk membuka activity lain (membuka URL dengan browser)
2. Tombol untuk menutup activity

Silahkan buka activity_main.xml, kemudian pastikan sesuai dengan code dibawah ini, usahakan ditulis ulang jangan copas.
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:orientation="vertical"
    tools:context="com.example.zetc0de.tugasactivity.MainActivity">

    <Button
        android:id="@+id/btn_start_activity"
        android:text="Open Other Activity"
        android:layout_marginBottom="@dimen/activity_vertical_margin"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"

    <Button
        android:id="@+id/btn_close"
        android:text="Close Acivity"
        android:layout_marginBottom="@dimen/activity_vertical_margin"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        
</LinearLayout>
```
Maka sekarang kita memiliki 2 tombol,
![]()


Setelah itu, silahkan buka MainActivity.java, dan pastikan sesuai dengan kode berikut:

```
package com.example.zetc0de.tugasactivity;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.content.Intent;
import android.net.Uri;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;


public class MainActivity extends AppCompatActivity implements View.OnClickListener {
    //Deklarasi komponen
    Button btnClose,btnStartActivity;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        // Inisialisasi komponen
        btnClose = (Button) findViewById(R.id.btn_close);
        btnStartActivity = (Button) findViewById(R.id.btn_start_activity);

        btnClose.setOnClickListener(this);
        btnStartActivity.setOnClickListener(this);

        Toast.makeText(this,"Activity: onCreate()", Toast.LENGTH_SHORT).show();
    }

    @Override
    protected void onStart() {
        super.onStart();
        Toast.makeText(this,"Activity: onStart()", Toast.LENGTH_SHORT).show();

    }

    @Override
    protected void onResume() {
        super.onResume();

        Toast.makeText(this,"Activity: onResume()", Toast.LENGTH_SHORT).show();

    }

    @Override
    protected void onPause() {
        super.onPause();

        Toast.makeText(this,"Activity: onPause()", Toast.LENGTH_SHORT).show();

    }

    @Override
    protected void onStop() {
        super.onStop();

        Toast.makeText(this,"Activity: onStop()", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onClick(View view) {
        switch (view.getId()) {
            case R.id.btn_start_activity:
                Intent bukaKiblat = new Intent(Intent.ACTION_VIEW);
                bukaKiblat.setData(Uri.parse("https://www.kiblat.net/"));
                startActivity(bukaKiblat);
                break;
            case R.id.btn_close:
                finish();
                break;

        }
    }
}

```
Setelah itu silahkan coba jalankan aplikasi dari menu run->Run(App)...
![]()


### References
- [Petanicode](https://www.petanikode.com/android-activity/)
- [Android Developer](https://developer.android.com/guide/topics/resources/more-resources.html#Dimension)
- [Stackoverflow](https://stackoverflow.com/questions/41368739/what-does-dimen-activity-vertical-margin-do)