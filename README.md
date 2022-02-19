
<!-- Logo -->
<p>
  <p align=left>
     <img src="eidiaeuro.png" width=200 height=100>
    <img src="euromed.png" width=200 height=100 align= right>
  </p>
 </p>
 

<!-- PROJECT LOGO -->
<br />
<div align="center">
    <h2 align="center">Programmation orientée objet (POO) :C++</h2>
  <h3 align="center"><span style="color:red">A COVID-19 Vaccination</span></h3>
    <img src="azs (1).jpg" alt="Logo" width="800" height="360">
</div>



* * *
## Introduction:
  1. Objectif
  2. La mise en œuvre nécessaire pour ce programme.
  3. Page de connexion principale (5 boutons peuvent effectuer différentes actions)
  4. les codes et les bibliothèques nécessaires à la compilation de ce programme                                          (pour chaque fichier).
  5. Pour la page utilisateur : (bouton s'inscrire)
  6. Pour la page utilisateur : (bouton de connexion) et boîte de dialogue suivante
  7. Pour la page d'administration : (bouton s'inscrire)
  8. Pour la page d'administration : (bouton de connexion) et boîte de dialogue suivante
  9. Pour la page principale (bouton quitter)


* * *

* * *
<p>
  <p align=left>
    <h1 align="left">1.	Objectif :</h2>
     </p>
 </p>
Cette application en tant que simulation peut aider les gens à planifier facilement un vaccin. Quand il y a un nouveau vaccin sorti et ouvert au public, il y a beaucoup de gens qui Veulent l’obtenir, mais il est difficile de le planifier parce que les gens ont un temps de travail différent, aussi Le nombre de vaccins est limité au début. Cette application aidera l’utilisateur à s’inscrire, choisir les vaccins et planifier leurs rendez-vous. Pour le système d’administration, l’administrateur peut vérifier les informations de l’utilisateur et être en mesure d’enregistrer, de modifier, de mettre à jour et de supprimer toutes informations dans le système.

<p>
  <p align=left>
     <h1 align="left">2.	La mise en œuvre nécessaire pour ce programme :</h2>
      <h3 align="left">DB (Data Base) Navigateur pour SQLite :</h2>
Stockez toutes les données dont nous avons besoin pour ce programme, il est inclus deux tables (table utilisateur, table admin)
<h3 align="left"> DB Structure:</h2>

![2022-02-06 04_22_22-Print Preview](https://user-images.githubusercontent.com/93819249/152666603-57bd9142-163e-4b69-954e-c711eb727caf.png)

  </p>
 </p>


* * *
* * *
<p>
  <p align=left>  <h1 align="left">3.	Page de connexion principale (5 boutons peuvent effectuer différentes actions) :</12></p>
 </p>


![2022-02-06 03_15_11-Login](https://user-images.githubusercontent.com/93819249/152665431-bf6fe4ba-3422-4e1a-9667-4992f8e28985.png)



* * *
<p>
  <p align=left>  <h1 align="left">4. bibliothèques nécessaires à la compilation de ce programme (pour chaque fichier):</12></p>
 </p>
 
 * * *
 <p>
  <p align=left>  <h1 align="left">A. main.cpp :</h></p>
  
  <p align=left>  <h4 align="left">
  
  ```cpp
#include "Login.h"

#include <QApplication>
#include <QTabWidget>
#include <QPushButton>

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);
    Login w;
    w.show();
    return a.exec();
}

  ```
  </h4></p>
  
   <p align=left>  <h1 align="left">B. Login.cpp:</></p>
  
  <p align=left>  <h4 align="left">
   
  ```cpp
#include "Login.h"
#include "ui_Login.h"
#include "checkk.h"
#include "ui_checkk.h"
#include "UserDialog1.h"
#include <QMessageBox>
#include "userMain.h"
#include "user.h"
#include "user.cpp"
#include <QSqlError>
#include <QSqlQuery>
#include <QDebug>
#include <QString>
#include <QException>
#include<QDebug>


Login::Login(QWidget *parent): QMainWindow(parent), ui(new Ui::Login){  
    ui->setupUi(this);
    TestDB();
    on_groupBox_toggled(0); //for set the login page invisible
    ui->pushButton_userRegister->setVisible(0); //for set the register button invisible for the normal users
}

Login::~Login(){
    delete ui;
}

// for action admin register....(admin_name,admin_pass,admin_id)
void Login::on_pushButton_adminRegister_clicked(){   
    Login conn;
    QString adminUsername, adminPassword, adminCode; //variable for stocke the inforation
//actions for get the information from the widgets
    adminUsername = ui->lineEdit_adminUsername->text(); 
    adminPassword = ui->lineEdit_adminPW->text();
    adminCode = ui->lineEdit_adminCode->text();
//test the db is open or no
    if(!connOpen()){
        qDebug() <<"Failed to open the database";
        return;
    }
    connOpen();   // open the database function
    QSqlQuery qry;
  //for add new admin account to db
    qry.prepare("insert into admins (admin_name,admin_pass,admin_id) values ('"+adminUsername+"','"+adminPassword+"','"+adminCode+"')" );
//show in qt command the information sent to db
    qDebug()<<qry.executedQuery();
  //message of the fonction
    if(qry.exec()) {
        QMessageBox::information(this,tr("Save"),tr("Admin Data Saved!"));
        conn.connClose();
    }else{
        QMessageBox::critical(this,tr("Error"),qry.lastError().text());
    }
}

// for action admin login select 3 columns from database
void Login::on_pushButton_adminLogin_clicked(){  
    int count = 0;
  
  //the same just for login admin
    try {
  //for get the information from th widgets we need name password and work code=id
        QString adminUsername = ui ->lineEdit_adminUsername->text();   // login and get the text of username and password form user inputs.
        QString adminPassword = ui->lineEdit_adminPW->text();
        QString adminCode = ui->lineEdit_adminCode->text();
        connOpen();
        QSqlQuery qry;
  //search if in db account with the same information of authentification
        qry.prepare("select* from admins where admin_name='"+adminUsername + "'and admin_pass = '"+adminPassword+"'and admin_id = '"+adminCode+"'");

        if(qry.exec()) {
            while(qry.next()){
                count++;
            }

            qDebug() << count;
            if(count==1){
                connClose();    // close the database before open next window
                this->hide();
                adminP1 = new AdminDialog1(this);
                adminP1->show();
            }else{
                throw("Error");
            }
        }
    }
  
  //give the status of the inforamation entred its wrong or not 
    catch (...) {
        ui->Status ->setText("Username or password or work code is NOT correct.");
        qDebug() << "Catch Error： Username or password or work code is NOT correct. ";
    }

}

// for action  user login select 2 columns
void Login::on_pushButton_userLogin_clicked(){  
    int count = 0;
  //the same just for login admin but this for users normal
  try {
        QString userUsername = ui ->lineEdit_userUsername->text();     // login and get the text of username and password form user inputs.
        QString userPassword = ui->lineEdit_userPW->text();
        connOpen();                                                    // open the database function, there are debug in the functions.
        QSqlQuery qry;
        qry.prepare("select* from users where name='"+userUsername+ "' and pass = '"+userPassword+"'" );

        if(qry.exec()) {
            while(qry.next()){
                count++;
            }
            qDebug() << count;
            if(count==1){
                ui->Status ->setText("Username and password is correct.");
                connClose();
                this->hide();
                userP1 = new UserDialog1(this);
                userP1->user_name = userUsername;
                userP1->pw = userPassword;

                qDebug() << userP1->user_name + " has logged in";
                userP1->show();
            }
            else if (count == 0) {
                throw("Error");
            }
        }
    }
    catch (...) {
        ui->Status ->setText("Username or password is NOT correct.");    }
}


void Login::on_pushButton_userRegister_clicked(){
    Login conn;
    User curUser;
  //the same with admin registration but for normal users
    curUser.username = ui->lineEdit_userUsername->text();
    curUser.password = ui->lineEdit_userPW->text();

    if(!connOpen()){
        qDebug() <<"Failed to open the database";
        return;
    }
    connOpen();
    QSqlQuery qry;
    qry.prepare("insert into users (name,pass) values ('"+curUser.username+"','"+curUser.password+"')" );
    if(qry.exec()) {
        QMessageBox::information(this,tr("Save"),tr("User Data Saved!"));
        conn.connClose();
    }else{
        QMessageBox::critical(this,tr("Error"),qry.lastError().text());
    }
}

//event of close
void Login::on_pushButton_loginQuit_clicked(){
    QMessageBox::StandardButton reply = QMessageBox::question(this,"Exit","Are you sure to quit the login page? ", QMessageBox::Yes| QMessageBox::No);
    if(reply == QMessageBox::Yes){
        QApplication::quit();
    }
}
  //test the db is connected or not and give u the status the test from the destination give in login.h
void Login::TestDB(){
    if(!connOpen()){
        ui->Status->setText("Failed to open the database!");
    }else{
        ui->Status->setText("Connected database........");
    }
}

void Login::on_groupBox_toggled(bool arg1)
{
  //admin page visible
    ui->groupBox->setVisible(arg1);
}

//action for set the page of admin and register button visible 
void Login::on_Adminbutton_clicked()
{
  //z is count if u give the code wrong 3 time u will be cannot log as admin
    z++;
    checkk D;
    auto repl = D.exec();
    auto e=D.Password();
  //if the password enter is correct 2001 u will be see the admin page and register button
    if(e==2001){
        ui->groupBox->setVisible(1);
        ui->pushButton_userRegister->setEnabled(1);
        ui->pushButton_userRegister->setVisible(1);
    }
    if(z==3){
        ui->Status->setText("You are not An Admin");
        ui->Adminbutton->setEnabled(0);
        ui->Status->setText("Connected database........");
        ui->Adminbutton->setVisible(0);
    }
}



   ```
  </h4></p>
<p align=left>  <h1 align="left">B. Login.h:</12></p>
  
  <p align=left>  <h4 align="left">
  
 ```cpp
#ifndef LOGIN_H
#define LOGIN_H
#include <QMainWindow>
#include "UserDialog1.h"
#include "AdminDialog1.h"
#include <QtSql>
#include <QtDebug>
#include <QFileInfo>
#include <QtSql>
#include <QtDebug>
#include <QFileInfo>

QT_BEGIN_NAMESPACE
namespace Ui { class Login; }
QT_END_NAMESPACE

class Login : public QMainWindow
{
    Q_OBJECT

public:
    QSqlDatabase mydb;

    void connClose(){
        mydb.close();
        mydb.removeDatabase(QSqlDatabase::defaultConnection);
    }
//for test is db connectsor no
    bool connOpen(){
        mydb = QSqlDatabase::addDatabase("QSQLITE");
        mydb.setDatabaseName("C:/Users/Vaccine.db");
        if(!mydb.open()){
            qDebug()<<("Failed to open the database!");
            return false;
        }else{
            qDebug()<<("Connected database........");
            return true;
        }
    }

public:
    Login(QWidget *parent = nullptr);
    ~Login();
    void TestDB();
    int z=0;


private slots:
    void on_pushButton_userLogin_clicked();
    void on_pushButton_adminLogin_clicked();
    void on_pushButton_userRegister_clicked();
    void on_pushButton_adminRegister_clicked();
    void on_pushButton_loginQuit_clicked();
    void on_groupBox_toggled(bool arg1);
    void on_Adminbutton_clicked();

private:
    Ui::Login *ui;
    UserDialog1 *userP1;
    AdminDialog1 *adminP1;
};
#endif // LOGIN_H


```
  </h4></p>
  
  ![2022-02-06 04_12_11-VaccineSchedule - Qt Creator](https://user-images.githubusercontent.com/93819249/152666371-c69fe399-27c2-4db6-ad0a-699833e9ef7e.png)

  
<p align=left>  <h1 align="left">C. User.cpp:</12></p>
  
  <p align=left>  <h4 align="left">
  
 ```cpp
  
#include "user.h"
#include "Login.h"
#include <iostream>
#include <QString>
using namespace std;

// Default constructor
User::User() {
  //and give the null if the place vide
    QString username = NULL,
            password = NULL,
            insurance = NULL,
            firstname = NULL,
            lastname = NULL,
            age = NULL,
            vaccine = NULL,
            shot = NULL,
            date = NULL,
            time = NULL;
}

// constructor for first login page, two variables
User::User(QString un, QString pw){
  /for the type and inhe
    this->username = un;
    this->password = pw;
    this->insurance = "0";
    this->firstname = "NULL",
            this->lastname = "NULL",
            this->age = "0",
            this->vaccine = "NULL",
            this->shot = "0",
            this->date = "xx-xx-xxxx",
            this->time = "0:00 - 0:00";
}

User::User(QString un, QString pw, QString insur, QString fn, QString ln, QString age, QString vacc, QString shot, QString date, QString time){
    this->username = un;
    this->password = pw;
    this->insurance = insur;
    this->firstname = fn;
    this->lastname = ln;
    this->age = age;
    this->vaccine = vacc;
    this->shot = shot;
    this->date = date;
    this->time = time;
}
  
```
</h4></p>
  

 <p align=left>  <h1 align="left">C. User.h:</12></p>
  
  <p align=left>  <h4 align="left">
  
 ```cpp
 #pragma once

#ifndef USER_H
#define USER_H
#include <iostream>
#include <QString>
using namespace std;

class User{
private:

public:
    QString username,password,insurance,firstname,lastname,age,vaccine,shot,date,time; //for the stock info

    // constructors
    User();
    User(QString un, QString pw);
    User(QString un, QString pw, QString insur, QString fn, QString ln, QString age, QString vacc, QString shot, QString date, QString time);


    // sets information
    void setUsername(QString un);
    void setPassword(QString pw);
    void setInsurance(QString insur);
    void setfirstname(QString fn);
    void setlastname(QString ln);
    void setAge(QString age);
    void setVaccine(QString vacc);
    void setShot(QString shot);
    void setDate(QString date);
    void setTime(QString time);

    // gets information
    QString getUsername();
    QString getPassword();
    QString getInsurance();
    QString getfirstname();
    QString getlastname();
    QString getAge();
    QString getVaccine();
    QString getShot();
    QString getDate();
    QString getTime();

};
 
 ```
  </h4></p>
  
  

<p align=left>  <h1 align="left">D. UserDialog1.cpp:</12></p>
 
 <p align=left>  <h4 align="left">
  
  
 ```cpp
#include <QDialog>
#include "ui_UserDialog1.h"
#include "UserDialog1.h"
#include <QPixmap>
#include <QMessageBox>
#include <QDebug>
#include "Login.h"
#include "user.h"

UserDialog1::UserDialog1(QWidget *parent) : QDialog(parent),ui(new Ui::UserDialog1){
    ui->setupUi(this);
    int w = ui->label_chooseVaccine->width();
    int h = ui->label_chooseVaccine->height();
    QPixmap pic1(":/img/vaccinePic1.png");
    ui->label_chooseVaccine->setPixmap(pic1.scaled(w,h,Qt::KeepAspectRatio));

    Login conn;     // create a new object
    if(!conn.connOpen()){
        ui->label_userP1->setText("Failed to open the database!");
        qDebug()<<"The database Not connected with UserDialog1 page.";
    }else{
        ui->label_userP1->setText("Connected database........");
        qDebug()<<"The database connected with UsserDialog1 page.";
    }

}
// google variables for save the data from different actions.
QString age;
QString vaccine;
QString shot;
QString date;
QString tim;

UserDialog1::~UserDialog1(){
    delete ui;
}


void UserDialog1::on_pushButton_userNextPage_clicked(){
    Login conn;
    User user;
  //for give the information 
    user.username = user_name;
    user.password = pw;
    user.insurance = ui->lineEdit_insurNum->text();
    user.firstname = ui->lineEdit_fName->text();
    user.lastname = ui->lineEdit_lName->text();
    user.age = age;
    user.vaccine = vaccine;
    user.shot = shot;
    user.date = ui->dateEdit->text();
    user.time = tim;

    if(!conn.connOpen()){
        qDebug() <<"Failed to open the database";
        return;
    }
    conn.connOpen();
    QSqlQuery qry;

    qry.prepare("update users set name='"+user.username+"',pass='"+user.password+"',asurance='"+user.insurance+"',firstname='"+user.firstname+"',lastname='"+user.lastname+"'"
             ",age='"+user.age+"',vaccin='"+user.vaccine+"',shote='"+user.shot+"',date='"+user.date+"',time='"+user.time+"' where name='"+user.username+"'");

    qDebug()<<qry.executedQuery();
    if(qry.exec()) {
        QMessageBox::information(this,tr("Save"),tr("Data Saved!"));
        conn.connClose();
    }else{
        QMessageBox::critical(this,tr("Error"),qry.lastError().text());
    }

    window()->hide();

    qDebug()<< user.username + " saved infomation: ";
    qDebug()<< user.username + " , " + user.password + " , " + user.insurance + " , " + user.firstname + " , " + user.lastname +
               " , " + user.age + " , " + user.vaccine + " , " + user.shot + " , " + user.date + " , " + user.time;

}

//for the type of the vaccine for specific cas
void UserDialog1::on_radioButton_Jonson_clicked(){
    vaccine = "Jonson & Johnson’s Janssen ";
    ui->radioButton_under18->setCheckable(1);
    ui->radioButton_under18->setVisible(1);
    ui->radioButton_secondShot->setVisible(0);
}

void UserDialog1::on_radioButton_moderna_clicked(){
    vaccine = "Moderna";
    ui->radioButton_under18->setCheckable(1);
    ui->radioButton_under18->setVisible(1);
    ui->radioButton_secondShot->setVisible(1);
}

void UserDialog1::on_radioButton_pfizer_clicked(){
    vaccine = "Pfizer-BioNTech";
    ui->radioButton_under18->setCheckable(0);
    ui->radioButton_under18->setVisible(0);
    ui->radioButton_secondShot->setVisible(1);
}


// for Radio Buttom, choose age, only can choose one
void UserDialog1::on_radioButton_under18_clicked(){
    age = "Under 18";
}

void UserDialog1::on_radioButton_over18_clicked(){
    age = "Between 18~50";
}

void UserDialog1::on_radioButton_over50_clicked(){
    age = "Over 50";
}

void UserDialog1::on_radioButton_over65_clicked(){
    age = "Over 65";
}


// for Radio Buttom, choose shot, first or second shot
void UserDialog1::on_radioButton_firstShot_clicked(){
    shot = "First shot";
}

void UserDialog1::on_radioButton_secondShot_clicked(){
    shot = "Seecond shot";
}

void UserDialog1::on_comboBox_hours_activated(const QString &arg1){
    tim = arg1;
}


```
</h4></p>
  
<p align=left>  <h1 align="left">D. UserDialog1.h:</12></p>
  
<p align=left>  <h4 align="left">  
  
  ```cpp
 #ifndef USERDIALOG1_H
#define USERDIALOG1_H

#include <QDialog>

namespace Ui {
class UserDialog1;
}

class UserDialog1 : public QDialog
{
    Q_OBJECT

public:

    explicit UserDialog1(QWidget *parent = nullptr);
    ~UserDialog1();

    QString user_name;
    QString pw;

private slots:

    void on_pushButton_userNextPage_clicked();

    void on_radioButton_Jonson_clicked();

    void on_radioButton_moderna_clicked();

    void on_radioButton_pfizer_clicked();


    void on_radioButton_under18_clicked();

    void on_radioButton_over18_clicked();

    void on_radioButton_over50_clicked();

    void on_radioButton_over65_clicked();


    void on_radioButton_firstShot_clicked();

    void on_radioButton_secondShot_clicked();


    void on_comboBox_hours_activated(const QString &arg1);

private:
    Ui::UserDialog1 *ui;

};

#endif // USERDIALOG1_H
  
  ```
  
  </h4></p>
  
  ![2022-02-06 04_14_42-ASHellos_A-COVID-19-Vaccination](https://user-images.githubusercontent.com/93819249/152666408-37eb51d8-edd3-4c16-9024-ed2d453f0b1c.png)

  
 <p align=left>  <h1 align="left"> E. AdminDialog1.cpp:</12></p>
  
<p align=left>  <h4 align="left">
  
  
```cpp
#include "AdminDialog1.h"
#include "ui_AdminDialog1.h"
#include "Login.h"
#include <QString>
#include <QMessageBox>
#include <QPixmap>
#include <QTableView>
#include <QListView>
#include <QLineEdit>
#include <QSqlTableModel>
#include <QComboBox>
#include "user.h"

AdminDialog1::AdminDialog1(QWidget *parent) :
    QDialog(parent),
    ui(new Ui::AdminDialog1){
    ui->setupUi(this);
}

AdminDialog1::~AdminDialog1(){
    delete ui;
}

void AdminDialog1::on_pushButton_loadInfo_clicked(){
    Login conn;
    QSqlQueryModel * modal = new QSqlQueryModel();  // create model
    conn.connOpen();
    //test db
    QSqlQuery* qry = new QSqlQuery(conn.mydb);

    qry->prepare("select name,pass,asurance,firstname,lastname,age,vaccin,shote,date,time from users");
    qry->exec();
    modal->setQuery(*qry);

    ui->listView->setModel(modal);
    ui->comboBox->setModel(modal);

    ui->tableView->setModel(modal);

    conn.connClose();
    qDebug()<<(modal->rowCount());

}

void AdminDialog1::on_comboBox_currentIndexChanged(const QString &arg1){   
    QString username = ui->comboBox->currentText();
    Login conn;
    if(!conn.connOpen()){
        qDebug() <<"Failed to open the database";
        return;
    }
    conn.connOpen();
    QSqlQuery qry;

    qry.prepare("select * from users where name='"+username+"'");
    qDebug()<<qry.executedQuery();

    if(qry.exec()) {
        while(qry.next()){
            ui->lineEdit_username->setText(qry.value(0).toString());
            ui->lineEdit_pw->setText(qry.value(1).toString());
            ui->lineEdit_insur->setText(qry.value(2).toString());
            ui->lineEdit_fName->setText(qry.value(3).toString());
            ui->lineEdit_lName->setText(qry.value(4).toString());
            ui->lineEdit_age->setText(qry.value(5).toString());
            ui->lineEdit_vaccine->setText(qry.value(6).toString());
            ui->lineEdit_shot->setText(qry.value(7).toString());
            ui->lineEdit_date->setText(qry.value(8).toString());
            ui->lineEdit_time->setText(qry.value(9).toString());
        }
        conn.connClose();
    }else{
        QMessageBox::critical(this,tr("Error"),qry.lastError().text());
    }
}


void AdminDialog1::on_tableView_activated(const QModelIndex &index){

    QString val = ui->tableView->model()->data(index).toString();
    Login conn;
    if(!conn.connOpen()){
        qDebug() <<"Failed to open the database";
        return;
    }
    conn.connOpen();
    QSqlQuery qry;
    qry.prepare("select * from users where name='"+val+"' or pass='"+val+"' or asurance='"+val+"' or firstname='"+val+"' or lastname='"+val+"'"
             " or age='"+val+"' or vaccin='"+val+"' or shote='"+val+"' or date='"+val+"' or time='"+val+"' ");

    if(qry.exec()) {

        while(qry.next()){
            ui->lineEdit_username->setText(qry.value(0).toString());
            ui->lineEdit_pw->setText(qry.value(1).toString());
            ui->lineEdit_insur->setText(qry.value(2).toString());
            ui->lineEdit_fName->setText(qry.value(3).toString());
            ui->lineEdit_lName->setText(qry.value(4).toString());
            ui->lineEdit_age->setText(qry.value(5).toString());
            ui->lineEdit_vaccine->setText(qry.value(6).toString());
            ui->lineEdit_shot->setText(qry.value(7).toString());
            ui->lineEdit_date->setText(qry.value(8).toString());
            ui->lineEdit_time->setText(qry.value(9).toString());
        }
        conn.connClose();
    }else{
        QMessageBox::critical(this,tr("Error"),qry.lastError().text());
    }
}


void AdminDialog1::on_listView_activated(const QModelIndex &index){

    QString val = ui->listView->model()->data(index).toString();
    Login conn;

    if(!conn.connOpen()){
        qDebug() <<"Failed to open the database";
        return;
    }
    conn.connOpen();
    QSqlQuery qry;

    qry.prepare("select * from users where name='"+val+"' ");

    qDebug()<<qry.executedQuery();
    if(qry.exec()) {
        while(qry.next()){
            ui->lineEdit_username->setText(qry.value(0).toString());
            ui->lineEdit_pw->setText(qry.value(1).toString());
            ui->lineEdit_insur->setText(qry.value(2).toString());
            ui->lineEdit_fName->setText(qry.value(3).toString());
            ui->lineEdit_lName->setText(qry.value(4).toString());
            ui->lineEdit_age->setText(qry.value(5).toString());
            ui->lineEdit_vaccine->setText(qry.value(6).toString());
            ui->lineEdit_shot->setText(qry.value(7).toString());
            ui->lineEdit_date->setText(qry.value(8).toString());
            ui->lineEdit_time->setText(qry.value(9).toString());
        }
        conn.connClose();
    }else{
        QMessageBox::critical(this,tr("Error"),qry.lastError().text());
    }
}

void AdminDialog1::on_pushButton_save_clicked(){  // insert
    Login conn;
    User user;
    user.username = ui->lineEdit_username->text();
    user.password = ui->lineEdit_pw->text();
    user.insurance = ui->lineEdit_insur->text();
    user.firstname = ui->lineEdit_fName->text();
    user.lastname = ui->lineEdit_lName->text();
    user.age = ui->lineEdit_age->text();
    user.vaccine = ui->lineEdit_vaccine->text();
    user.shot = ui->lineEdit_shot->text();
    user.date = ui->lineEdit_date->text();
    user.time = ui->lineEdit_time->text();
    if(!conn.connOpen()){
        qDebug() <<"Failed to open the database";
        return;
    }
    conn.connOpen();
    QSqlQuery qry;
    qry.prepare("insert into users (name,pass,asurance,firstname,lastname,age,vaccin,shote,date,time) "
     "values ('"+user.username+"','"+user.password+"','"+user.insurance+"','"+user.firstname+"','"+user.lastname+"','"+user.age+"','"+user.vaccine+"','"+user.shot+"','"+user.date+"','"+user.time+"' )" );


    if(qry.exec()) {
        QMessageBox::information(this,tr("Save"),tr("Data Saved!"));
        conn.connClose();
    }else{
        QMessageBox::critical(this,tr("Error"),qry.lastError().text());
    }

}


void AdminDialog1::on_pushButton_update_clicked(){  // update
    Login conn;
    User user;
    user.username = ui->lineEdit_username->text();
    user.password = ui->lineEdit_pw->text();
    user.insurance = ui->lineEdit_insur->text();
    user.firstname = ui->lineEdit_fName->text();
    user.lastname = ui->lineEdit_lName->text();
    user.age = ui->lineEdit_age->text();
    user.vaccine = ui->lineEdit_vaccine->text();
    user.shot = ui->lineEdit_shot->text();
    user.date = ui->lineEdit_date->text();
    user.time = ui->lineEdit_time->text();

    if(!conn.connOpen()){
        qDebug() <<"Failed to open the database";
        return;
    }
    conn.connOpen();
    QSqlQuery qry;

    qry.prepare("update users set name='"+user.username+"',pass='"+user.password+"',asurance='"+user.insurance+"',firstname='"+user.firstname+"',lastname='"+user.lastname+"'"
             ",age='"+user.age+"',vaccin='"+user.vaccine+"',shote='"+user.shot+"',date='"+user.date+"',time='"+user.time+"' where name='"+user.username+"'" );


    if(qry.exec()) {
        QMessageBox::information(this,tr("Edit"),tr("Data Updated!"));
        conn.connClose();
    }else{
        QMessageBox::critical(this,tr("Error"),qry.lastError().text());
    }



}


void AdminDialog1::on_pushButton_delete_clicked(){ // delete
    Login conn;
    User user;
    user.username = ui->lineEdit_username->text();   //only need a unique variable

    if(!conn.connOpen()){
        qDebug() <<"Failed to open the database";
        return;
    }
    conn.connOpen();
    QSqlQuery qry;

    qry.prepare("Delete from users where name='"+user.username+"'");

    if(qry.exec()) {
        QMessageBox::information(this,tr("Delete"),tr("Data Deleted!"));
        conn.connClose();
    }else{
        QMessageBox::critical(this,tr("Error"),qry.lastError().text());
    }

}

void AdminDialog1::on_pushButton_quit_clicked(){
    QMessageBox::StandardButton reply = QMessageBox::question(this,"Exit","Are you sure to quit the login page? ", QMessageBox::Yes| QMessageBox::No);
    if(reply == QMessageBox::Yes){
        QApplication::quit();
    }else{
        qDebug() << "Answer 'NO' Button is clicked";
    }
}

  ```
 </12></p> 
  
 </p>
 
 <p>
  <p align=left>  <h1 align="left"> E. AdminDialog1.h:</12></p>
  
  <p align=left>  <h4 align="left">
  
  ```cpp
 #ifndef ADMINDIALOG1_H
#define ADMINDIALOG1_H

#include <QDialog>


namespace Ui {
class AdminDialog1;
}

class AdminDialog1 : public QDialog
{
    Q_OBJECT

public:

    explicit AdminDialog1(QWidget *parent = nullptr);
    ~AdminDialog1();

private slots:
    void on_pushButton_loadInfo_clicked();

    void on_comboBox_currentIndexChanged(const QString &arg1);

    void on_tableView_activated(const QModelIndex &index);

    void on_listView_activated(const QModelIndex &index);

    void on_pushButton_update_clicked();

    void on_pushButton_save_clicked();

    void on_pushButton_delete_clicked();

    void on_pushButton_quit_clicked();

private:
    Ui::AdminDialog1 *ui;

};

#endif // ADMINDIALOG1_H
  
  ```
  </h4></p>
  
 </p>
 
  ![2022-02-06 04_15_17-VaccineSchedule - Qt Creator](https://user-images.githubusercontent.com/93819249/152666410-69355d26-7c08-4a6f-9a64-b7b8de98fcee.png)

  
  <p>
  <p align=left>  <h1 align="left"> F. Check.cpp:</12></p>
  
  For test the user is admin not to give the permission of add new users **note the password is 2001**
  <p align=left>  <h4 align="left">
  
  ![2022-02-06 03_30_23-Login](https://user-images.githubusercontent.com/93819249/152665642-61bbbf18-5b16-44e4-8a9c-bc35695eb803.png)

  
  ```cpp
#include "checkk.h"
#include "ui_checkk.h"

checkk::checkk(QWidget *parent) :
    QDialog(parent),
    ui(new Ui::checkk)
{
    ui->setupUi(this);

}

checkk::~checkk()
{
    delete ui;
}

int checkk::Password(){
    QString input = ui->lineEdit->text();
    int integer_value = ui->lineEdit->text().toInt();
    return integer_value;
}
  
  ```
  </h4></p>
  
 </p>
 
 * * *
  
 <p>
  <p align=left>  <h1 align="left"> 5. Pour la page utilisateur : (Button Register):</h1></p>
 </p>
 
 Pour les personnes qui utilisent ce système et qui se connectent pour la première fois. Vous devez d’abord entrer deux choses, et faire 
ils sont tous corrects, et se souviennent aussi d’eux. Pour le nom d’utilisateur et le mot de passe, il peut s’agir d’une chaîne, d’un caractère, 
nombre. Le nom d’utilisateur est Unique.


Une fois que l’utilisateur a entré deux informations, il enregistrera le nom d’utilisateur et le mot de passe dans la base de données. 
de retour dans le système. Donc, la prochaine fois, cet administrateur a ses informations enregistrées dans la base de données.

 ## Manuels d’utilisation et de logiciels
 
![aa](https://user-images.githubusercontent.com/93819249/152665776-7d264e13-c2b7-4896-9a93-1e16a591c647.png)


 <p>
  <p align=left>  <h1 align="left"> 6. Pour la page utilisateur : (Button Login) et boîte de dialogue suivante</h1></p>
 </p>
 Pour les personnes qui utilisent avec ce système, vous devez d’abord entrer deux choses et vous assurer que 
le nom d’utilisateur et le mot de passe sont corrects en même temps. 

Ensuite, après avoir cliqué sur le bouton de connexion, les informations correspondront à la base de données. 
Sinon, il affichera une erreur jusqu’à ce que vous saisissiez le nom d’utilisateur et le mot de passe corrects.

#### cas 1: on entre le nom d'utilisateur n'est pas correct:
![az](https://user-images.githubusercontent.com/93819249/152665825-010235dd-cf37-430a-beec-59f71190d65e.png)

#### cas 2:on assure le nom d’utilisateur et le mot de passe sont corrects en même temps.
On Pasee directement à d'autres interface:

![2022-02-06 03_42_50-Vaccine information page](https://user-images.githubusercontent.com/93819249/152665847-0c1cf8a5-172b-417e-add1-9cc79857818e.png)

Vous pouvez commencer à construire et enregistrer votre imformation comme ceci:

![AAA](https://user-images.githubusercontent.com/93819249/152665924-4c5b1e39-70f2-46bd-bb91-3e39f828c6df.png)


Après avoir cliqué sur le Botton suivant, toutes vos informations sont enregistrées dans le système de base de données.
![2022-02-06 03_49_26-DB Browser for SQLite - C__Users_adilr_Desktop_VaccineSchedule_Vaccine db](https://user-images.githubusercontent.com/93819249/152665949-e56363eb-6684-4ef8-ac20-1df93447de88.png)



 <p>
  <p align=left>  <h1 align="left"> 7.Pour la page d'administration : (Button Register)</h1></p>
 </p>
Pour les personnes qui travaillent en tant qu’administrateur avec ce système, et la première connexion. Vous devez entrer 
trois choses d’abord, et faites-les toutes correctes, et souvenez-vous aussi d’elles. Pour le nom d’utilisateur 
et Mot de passe, il peut s’agir d’une chaîne, d’un caractère, d’un nombre. Mais pour le code de travail, il doit s’agir d’un 
nombre. Le nom d’utilisateur est Unique.
Après avoir entré l’administrateur pour ces trois informations, il enregistrera le nom d’utilisateur, le mot de passe et 
code de travail dans la base de données dans le système. Donc la prochaine fois, cet admin a son/elle 
les informations enregistrées dans la base de données. Cela ressemblera à ceci:
  
![adm](https://user-images.githubusercontent.com/93819249/152666034-fc981724-5d21-4626-9875-5d2ba992f402.png)


 <p>
  <p align=left>  <h1 align="left"> 8.Pour la page d'administration : (Button login)</h1></p>
 </p>

Si vous avez fait les choses correctes comme vous vous êtes inscrit à la première fois. La deuxième boîte de dialogue sautera 
comme ça.
![admin](https://user-images.githubusercontent.com/93819249/152666106-4db66932-2948-4f4b-8f79-c3fae498fb75.png)


Ensuite, après avoir cliqué sur le bouton « **Load Data** », toutes les données de la base de données seront 
télécharger sur la vue de tableau( la zone noire et aussi chaque textEditLine.
En outre, il y a trois boutons qui sont cliquables.

**Save button:** Pour enregistrer toute l’imformation du côté de l’administrateur. Entrez toute l’imformation 
sur le texte a fait une ligne, puis appuyez sur le bouton Enregistrer. Le rendez-vous vaccinal de l’utilisateur sera 
sauvé.

**Update button:** Pour modifier et mettre à jour l’imformation à partir du système de base de données, au cas où, 
n’importe qui veut changer l’imformation qu’il a enregistrée auparavant.

**Delete button:** pour supprimer toute imformation que l’utilisateur a enregistrée auparavant.
Votre écran ressemblera à ceci:
![admins](https://user-images.githubusercontent.com/93819249/152666110-a90b76a7-d2ff-4c52-89a9-1939873697a3.png)


une fois on clique sur le bouton **saved**.les information sont enregister a cote de l'administrateur:
![2022-02-06 03_58_44-ASHellos_A-COVID-19-Vaccination](https://user-images.githubusercontent.com/93819249/152666136-42d7189c-f399-4650-9844-ac5258b56edf.png)

 <p>
  <p align=left>  <h1 align="left"> 9.Pour la page principale (bouton quitter)</h1></p>
 </p>
Une fois que l’utilisateur a cliqué sur le bouton Quitter, la boîte Qmessage saute:Oui pour **quitter** le système, non pour **rester**.

![2022-02-06 04_00_08-DB Browser for SQLite - C__Users_adilr_Desktop_VaccineSchedule_Vaccine db](https://user-images.githubusercontent.com/93819249/152666155-f9a45f81-6cfb-4df5-a8ca-cef6d812c91c.png)


* * *
![titre](https://user-images.githubusercontent.com/93833171/151644405-8e4e79c2-22b6-4cf1-a974-0bf2bf4eb3d2.jpg)
