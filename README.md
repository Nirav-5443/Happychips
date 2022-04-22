Explanation of Code
DbHelper Page for Database and Table creation



Class DbHelper(context:Context):SQLiteOpenHelper(context,"STUDENTDB",null,1) {
    override fun onCreate(db: SQLiteDatabase?)
 {
        db?.execSQL("CREATE TABLE LOGIN(USERID TEXT, PASSWORD TEXT)")
        db?.execSQL("CREATE TABLE TEACHERINFO(USERID TEXT,NAME TEXT, DESIGNATION TEXT, COURSE TEXT, SUBJECT TEXT, MOBILE TEXT, EMAIL TEXT)")
        db?.execSQL("CREATE TABLE STUDENTINFO(USERID TEXT,RNO TEXT,NAME TEXT)")

    }

    override fun onUpgrade(db: SQLiteDatabase?, p1: Int, p2: Int) {

    }
}







SignUp Page

signup2.setOnClickListener {

//Check Empty
            if (useridsignup.text.toString() == "" || passwordsignup.text.toString() == "" || passwordsignup2.text.toString() == "") {
                var toast = Toast.makeText(
                    this,
                    "Enter UserId, Password and Re-Enter Password",
                    Toast.LENGTH_SHORT
                )
                toast.setGravity(Gravity.TOP, 0, 300)
                toast.show()
                useridsignup.setText("")
                passwordsignup.setText("")
                passwordsignup2.setText("")
                useridsignup.requestFocus()
            } else if (passwordsignup.text.toString() != passwordsignup2.text.toString()) {

                var toast = Toast.makeText(this, "Passwords Must be Same...", Toast.LENGTH_SHORT)
                toast.setGravity(Gravity.TOP, 0, 300)
                toast.show()
                passwordsignup.setText("")
                passwordsignup2.setText("")
                passwordsignup.requestFocus()

            } else {
               

//check for existing userid

 var helper = DbHelper(applicationContext)
                var db: SQLiteDatabase = helper.writableDatabase
                var check = arrayOf(useridsignup.text.toString())
                var rs: Cursor = db.rawQuery("SELECT * FROM LOGIN WHERE USERID=?", check)
                rs.requery()
                if (rs.moveToNext()) {
                    Toast.makeText(this, "User Exist, Try with new UserID", Toast.LENGTH_LONG).show()
                    useridsignup.setText("")
                    passwordsignup.setText("")
                    passwordsignup2.setText("")
                    useridsignup.requestFocus()
                } else {
                  
//inserting in database

  var beforeinsert = rs.count
                    var cv = ContentValues()
                    cv.put("USERID", useridsignup.text.toString())
                    cv.put("PASSWORD", passwordsignup.text.toString())
                    db.insert("LOGIN", null, cv)
                    rs.requery()
                    var afterinsert = rs.count
                    if (afterinsert > beforeinsert) {
                        Toast.makeText(this, "Record Inserted Sucessfully", Toast.LENGTH_LONG)
                            .show()
                        startActivity(Intent(this, Login::class.java))

                    } else {
                        Toast.makeText(this, "Record Not Inserted", Toast.LENGTH_LONG).show()

                    }

                }
            }

        }

    }
}















Login Page


//connection variables
var helper = DbHelper(applicationContext)
var db: SQLiteDatabase = helper.writableDatabase

login.setOnClickListener {

//check for ID and PW
           
 
if(userid.text.toString() !="" && password.text.toString() != "")
            {
                var etuid = userid.text.toString()
                var etupw = password.text.toString()
                var check = arrayOf(etuid,etupw)
                var rs: Cursor = db.rawQuery("SELECT * FROM LOGIN WHERE USERID=? AND PASSWORD=?",check)
                rs.requery()
                if(rs.moveToNext())
                {
                    var intent = Intent(this, AddTeacherDetails::class.java)
                    intent.putExtra("USER",etuid)
                    startActivity(intent)
                }
                else
                {
                    var toast = Toast.makeText(this,"Enter Correct Credentials to Login",Toast.LENGTH_SHORT)
                    toast.setGravity(Gravity.TOP,0,300)
                    toast.show()
                    userid.setText("")
                    password.setText("")
                    userid.requestFocus()
                }
            }

            else if(userid.text.toString() == "" || password.text.toString() == "")
            {
                var toast = Toast.makeText(this,"Enter UserId and Password Both",Toast.LENGTH_SHORT)
                toast.setGravity(Gravity.TOP,0,300)
                toast.show()
                userid.requestFocus()
            }

            else
            {
                var toast = Toast.makeText(this,"Enter Correct Credentials to Login",Toast.LENGTH_SHORT)
                toast.setGravity(Gravity.TOP,0,300)
                toast.show()
                userid.setText("")
                password.setText("")
                userid.requestFocus()
            }


        }
        signup.setOnClickListener {
            startActivity(Intent(this, SignUp::class.java))

        }
    }
}










Add Teacher Details Page / Add Manager Details

//Connection Variables

var helper = DbHelper(applicationContext)
        var db: SQLiteDatabase = helper.writableDatabase
        var intent = getIntent()
        var userid = intent.getStringExtra("USER")

//Check and Fetch the records

        var check = arrayOf(userid)
        var rs: Cursor = db.rawQuery("SELECT * FROM TEACHERINFO WHERE USERID=?", check)
        rs.requery()
        if (rs.moveToNext()) {
            name.setText(rs.getString(1).toString())
            designation.setText(rs.getString(2))
            course.setText(rs.getString(3))
            subject.setText(rs.getString(4))
            mobile.setText(rs.getString(5))
            email.setText(rs.getString(6))
        }
        else {
            name.setHint("Enter Name")
            designation.setHint("Enter Designation")
            course.setHint("Enter Course")
            subject.setHint("Enter Subject")
            mobile.setHint("Enter Mobile No")
            email.setHint("Enter Email")


        }
       

//On Click save t he record

 btnupdate.setOnClickListener {
            if(name.text.toString() !="" && designation.text.toString() !="" && course.text.toString() !="" && subject.text.toString() !="" && mobile.text.toString() !="" && email.text.toString() !="")
            {
                var cv = ContentValues()
                cv.put("USERID",userid)
                cv.put("NAME", name.text.toString())
                cv.put("DESIGNATION", designation.text.toString())
                cv.put("COURSE", course.text.toString())
                cv.put("SUBJECT", subject.text.toString())
                cv.put("MOBILE", mobile.text.toString())
                cv.put("EMAIL", email.text.toString())
                db.insert("TEACHERINFO",null,cv)
                rs.requery()
                Toast.makeText(this, "Teacher Information Updated", Toast.LENGTH_LONG).show()
                var intent = Intent(this,Home::class.java)
                intent.putExtra("USER",userid)
                startActivity(intent)
            }
            else
            {
                Toast.makeText(this, "Enter All details", Toast.LENGTH_LONG).show()
            }

        }
        btnhome.setOnClickListener { var intent = Intent(this,Home::class.java)
            intent.putExtra("USER",userid)
            startActivity(intent) }
        btnstudent.setOnClickListener { var intent = Intent(this,AddStudentInfo::class.java)
            intent.putExtra("USER",userid)
            startActivity(intent) }
     }
}









Home Page


var helper = DbHelper(applicationContext)
        var db: SQLiteDatabase = helper.writableDatabase
        var intent = getIntent()
        var userid = intent.getStringExtra("USER")
        var check = arrayOf(userid)
        var rs: Cursor = db.rawQuery("SELECT * FROM TEACHERINFO WHERE USERID=?", check)
        rs.requery()
        if (rs.moveToNext()) {
            tname.text = rs.getString(1)
            tdesi.text = rs.getString(2)
            tcourse.text = rs.getString(3)
            tsubject.text = rs.getString(4)
            tmobile.text = rs.getString(5)
            temail.text = rs.getString(6)

        }
        else {
            tname.text = "No Record"
            tdesi.text = "No Record"
            tcourse.text = "No Record"
            tsubject.text = "No Record"
            tmobile.text = "No Record"
            temail.text = "No Record"


        }

        friendsbtn.setOnClickListener {
            var intent = Intent(this,AllStudents::class.java)
            intent.putExtra("USER",userid)
            startActivity(intent)



    }
        logoutbtn.setOnClickListener {
            startActivity(Intent(this, Login::class.java))
        }
        addstudbtn.setOnClickListener { var intent = Intent(this,AddStudentInfo::class.java)
            intent.putExtra("USER",userid)
            startActivity(intent)  }
    }

}





Add StudentInfo / Employeeinfo


var helper = DbHelper(applicationContext)
        var db: SQLiteDatabase = helper.writableDatabase
        var intent = getIntent()
        var userid = intent.getStringExtra("USER")

// Check for Student Exist or not   
     btnsave.setOnClickListener {
            var check = arrayOf(userid, rno.text.toString())
            var rs:Cursor = db.rawQuery("SELECT * FROM STUDENTINFO WHERE USERID=? AND RNO=?", check)
            rs.moveToFirst()
            if(rs.moveToNext()) {
                Toast.makeText(this, "Already Entered", Toast.LENGTH_LONG).show()
                rno.setText("")
                sname.setText("")
                rno.requestFocus()
            } else {
                var beforeinsert = rs.count
                var cv = ContentValues()
                cv.put("USERID", userid)
                cv.put("RNO", rno.text.toString())
                cv.put("NAME", sname.text.toString())
                db.insert("STUDENTINFO", null, cv)
                rs.requery()
                var afterinsert = rs.count
                if (afterinsert > beforeinsert) {
                    Toast.makeText(this, "Record Inserted Sucessfully", Toast.LENGTH_LONG)
                        .show()
                    rno.setText("")
                    sname.setText("")
                    rno.requestFocus()

                } else {
                    Toast.makeText(this, "Record Not Inserted", Toast.LENGTH_LONG).show()

                }


            }

        }
        btnview.setOnClickListener {
            var intent = Intent(this, AllStudents::class.java)
            intent.putExtra("USER",userid)

            startActivity(intent)
        }
        btnlogout.setOnClickListener { startActivity((Intent(this,Login::class.java))) }
    }
}











Custom Adapter for List View 


class CustomAdapter(
    var allstudents: AllStudents,
    var rno: ArrayList<String>,
    var name: ArrayList<String>
):BaseAdapter() {
    override fun getCount(): Int {
       return rno.size
    }

    override fun getItem(p0: Int): Any {
        return p0
    }

    override fun getItemId(p0: Int): Long {
        return p0.toLong()
    }

    override fun getView(p0: Int, p1: View?, p2: ViewGroup?): View {
        var inview = LayoutInflater.from(allstudents).inflate(R.layout.listitems,p2, false)
        var rn = inview.findViewById<TextView>(R.id.tvrnolist)
        var nm = inview.findViewById<TextView>(R.id.tvnamelist)
            rn.text = rno[p0]
            nm.text = name[p0]
        return inview
    }

}
    
    
    
    
    
    
    
    
    
All Students / Employees
    
    

var helper = DbHelper(applicationContext)
        var db: SQLiteDatabase = helper.writableDatabase
        var intent = getIntent()
        var userid = intent.getStringExtra("USER")
        var check = arrayOf(userid)
        var rs: Cursor = db.rawQuery("SELECT * FROM STUDENTINFO WHERE USERID=?",check)
        rs.moveToFirst()
        if(rs.moveToNext()) {
            val rno = ArrayList<String>()
            val name = ArrayList<String>()
            rs.moveToFirst()




//Create an Array to be shared           
 while (rs.moveToNext()) {
                rno.add(rs.getString(1))
                name.add(rs.getString(2))
            }
            var myadapter = CustomAdapter(this, rno, name)
            lview.adapter = myadapter
        }
        else
        {
            Toast.makeText(this, "No Records Found", Toast.LENGTH_LONG).show()
            var intent = Intent(this,AddStudentInfo::class.java)
            intent.putExtra("USER",userid)
            startActivity(intent)
        }



    }
}
    
    
    
    
    
    
    

all student xmls
    
    
    
<TextView
    android:id="@+id/tvrno"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginStart="62dp"
    android:layout_marginTop="36dp"
    android:text="Roll No"
    android:textStyle="bold"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent" />

<TextView
    android:id="@+id/tvname"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginTop="36dp"
    android:layout_marginEnd="104dp"
    android:text="Name"
    android:textStyle="bold"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintTop_toTopOf="parent" />

<ListView
    android:id="@+id/lview"
    android:layout_width="0dp"
    android:layout_height="518dp"
    android:layout_marginBottom="1dp"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintStart_toStartOf="parent" />


    
    
    
    
    

home or teacher details xml
    
    
    
<FrameLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent">

            <ImageView
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:scaleType="fitXY"
                android:src="@drawable/background"
                />
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:layout_margin="3dp"
                android:orientation="vertical"
                android:layout_gravity="center_horizontal">
                <LinearLayout
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:orientation="horizontal"
                    android:layout_gravity="center_horizontal">

                </LinearLayout>


            <LinearLayout
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:orientation="horizontal"
                android:weightSum="1"
                android:layout_margin="5dp" android:padding="5dp">
                <TextView
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:text="Name:"
                    android:layout_weight="0.3"
                    android:textColor="@color/black"
                    android:layout_marginRight="10dp"
                    android:textSize="20dp"/>
                <TextView
                    android:id="@+id/tname"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:text=""
                    android:textColor="@color/black"
                    android:layout_weight="0.7"
                    android:textStyle="bold"
                    android:textSize="20dp"/>


            </LinearLayout>
                <LinearLayout
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:orientation="horizontal"
                    android:weightSum="1"
                    android:layout_margin="5dp" android:padding="5dp">
                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text="Designation:"
                        android:layout_weight="0.3"
                        android:textColor="@color/black"
                        android:layout_marginRight="10dp"
                        android:textSize="20dp"/>
                    <TextView
                        android:id="@+id/tdesi"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text=""
                        android:textColor="@color/black"
                        android:layout_weight="0.7"
                        android:textStyle="bold"
                        android:textSize="20dp"/>


                </LinearLayout>
                <LinearLayout
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:orientation="horizontal"
                    android:weightSum="1"
                    android:layout_margin="5dp" android:padding="5dp">
                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text="Course:"
                        android:layout_weight="0.3"
                        android:textColor="@color/black"
                        android:layout_marginRight="10dp"
                        android:textSize="20dp"/>
                    <TextView
                        android:id="@+id/tcourse"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text=""
                        android:textColor="@color/black"
                        android:layout_weight="0.7"
                        android:textStyle="bold"
                        android:textSize="20dp"/>


                </LinearLayout>
                <LinearLayout
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:orientation="horizontal"
                    android:weightSum="1"
                    android:layout_margin="5dp" android:padding="5dp">
                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text="Subject:"
                        android:layout_weight="0.3"
                        android:textColor="@color/black"
                        android:layout_marginRight="10dp"
                        android:textSize="20dp"/>
                    <TextView
                        android:id="@+id/tsubject"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text=""
                        android:textColor="@color/black"
                        android:layout_weight="0.7"
                        android:textStyle="bold"
                        android:textSize="20dp"/>


                </LinearLayout>
                <LinearLayout
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:orientation="horizontal"
                    android:weightSum="1"
                    android:layout_margin="5dp" android:padding="5dp">
                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text="Mobile:"
                        android:layout_weight="0.3"
                        android:textColor="@color/black"
                        android:layout_marginRight="10dp"
                        android:textSize="20dp"/>
                    <TextView
                        android:id="@+id/tmobile"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text=""
                        android:textColor="@color/black"
                        android:layout_weight="0.7"
                        android:textStyle="bold"
                        android:textSize="20dp"/>


                </LinearLayout>
                <LinearLayout
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:orientation="horizontal"
                    android:weightSum="1"
                    android:layout_margin="5dp" android:padding="5dp">
                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text="Email:"
                        android:layout_weight="0.3"
                        android:textColor="@color/black"
                        android:layout_marginRight="10dp"
                        android:textSize="20dp"/>
                    <TextView
                        android:id="@+id/temail"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text=""
                        android:textColor="@color/black"
                        android:layout_weight="0.7"
                        android:textStyle="bold"
                        android:textSize="20dp"/>


                </LinearLayout>
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="horizontal"
                    android:layout_margin="5dp" android:weightSum="1">
                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"

                        android:textColor="@color/black"
                        android:textStyle="bold"
                        android:textSize="20sp"
                        android:layout_weight="0.3"/>

                    <TextView

                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_weight="0.7"

                        android:textSize="20dp" />
                </LinearLayout>
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="horizontal"
                    android:layout_margin="5dp" android:weightSum="1">
                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"

                        android:textColor="@color/black"
                        android:textStyle="bold"
                        android:textSize="20sp"
                        android:layout_weight="0.3"/>

                    <TextView

                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_weight="0.7"

                        android:textSize="20dp" />
                </LinearLayout>
                <LinearLayout
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:weightSum="1"

                    android:layout_gravity="center_horizontal">


                    <Button
                        android:id="@+id/friendsbtn"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="All Stud"
                        android:layout_margin="5dp"/>
                    <Button
                        android:id="@+id/logoutbtn"
                        android:layout_margin="5dp"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="LOGOUT"/>

                    <Button
                        android:id="@+id/addstudbtn"
                        android:layout_margin="5dp"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="ADD STUD"/>
                </LinearLayout>
            </LinearLayout>





        </FrameLayout>

    
    
    
    
    
    
listitems xml

    
    
    
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" android:layout_margin="10dp">

    <TextView
        android:id="@+id/tvrnolist"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="89dp"

        android:text="Rno"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <TextView
        android:id="@+id/tvnamelist"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginEnd="138dp"
        android:text="Name"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
