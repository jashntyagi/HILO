# HILO
#include<iostream>
#include<string.h>
#include<fstream>
#include<conio.h>
#include<dos.h>
#include<stdlib.h>
#include<stdio.h>
const int totchan=7;
class scores
{
char playername[20];
int playerscore;
public:
int retscore()
{
return playerscore;
}
int retname(char n[20])
{
if(strcmpi(playername,n)==0)
return 1;
else
return -1;
}
void getinfo(int a)
{
gets(playername);
playerscore=a;
}
void modifyname()
{
gets(playername);
}
void showinfo()
{
cout<<"\n"<<playername<<": "<<playerscore;
}
};
void intro()
{
int i,j;
clrscr();
textcolor(GREEN);
gotoxy(35,12);cprintf("HIGH-LOW");
for(i=1,j=1;i<26,j<77;j++,i++)
{
textcolor(GREEN);
gotoxy(1,i);cprintf("±");
gotoxy(j,1);cprintf("±");
gotoxy(79,i);cprintf("±");
gotoxy(j,25);cprintf("±");
delay(15);
}
}

void main()
{
clrscr();
scores s;
int chances,score,chancescore,number,guess,i,j,opt;
char ans;
intro();
fstream info;
do
{
clrscr();
textcolor(YELLOW);gotoxy(27, 7);cprintf("Welcome to the High/Low game.");
textcolor(YELLOW);gotoxy(35, 9);cprintf("1.Play Game");
textcolor(YELLOW);gotoxy(32, 10);cprintf("2.Show HIGHSCORES");
textcolor(YELLOW);gotoxy(34, 11);cprintf("3.Delete Score");
textcolor(YELLOW);gotoxy(33,12);cprintf("4.Modify Records");
textcolor(YELLOW);gotoxy(36, 13);cprintf("5.Exit\n");
cin>>opt;
switch(opt)
{
case 1:
{
clrscr();
do
{
clrscr();
chances=0;score=0;
textcolor(YELLOW);gotoxy(19, 6);cprintf("I will pick a random number from 0
to 100.");
textcolor(YELLOW);gotoxy(23, 7);cprintf("You must try to guess the number.");
randomize();
number=(int)(rand()%100);
chancescore=100/totchan;
do
{
gotoxy(10,10);cprintf("What is your guess? (0 to 100)");
gotoxy(10,11);cin>>guess;
if((guess<0)||(guess>100))
{
gotoxy(10,12);cprintf("Sorry, but your guess
");cout<<guess;cprintf(" must be from 0 to 100.");
}
else if(guess < number)
{
gotoxy(10,12);cout<<guess;cprintf(" is low.Try a higher number.");
}
else if(guess > number)
{
gotoxy(10,12);cout<<guess;cprintf(" is high.Try a lower number.");
}
else
{
textcolor(GREEN);gotoxy(24,16);cout<<guess;cprintf(" is correct.
Congratulations!");
score=chancescore*(totchan-chances);
textcolor(GREEN);gotoxy(28,17);cprintf("Your score is ");cout<<score;
break;
}
chances++;
if(guess!=number)
gotoxy(10,13);cprintf("Now you have ");cout<<totchan-chances;cprintf("
chances left.");
if(chances==totchan)
{
textcolor(RED);gotoxy(18,16);cprintf("Only
");cout<<totchan;cprintf(" chances are allowed.Better luck next
time");
textcolor(RED);gotoxy(29,17);cprintf("The actual number was
");cout<<number;

break;
}
}while (guess!=number);
textcolor(YELLOW);gotoxy(25,18);cprintf("Thank you for playing High/Low!");
textcolor(YELLOW);gotoxy(27,20);cprintf("Please enter your name: ");
info.open("playerinfo.dat",ios::binary|ios::app);
s.getinfo(score);
info.write((char*)&s, sizeof(s));
info.close();
textcolor(YELLOW);gotoxy(21,21);cprintf("Do you want to play the game
again? (y/n)...");
gotoxy(27,22);cin>>ans;
}while(ans=='y' || ans=='Y');
break;
}
case 2:
{
clrscr();
cout<<"HIGHSCORES";
int i=0,j,min,loc,nor;
scores sc[255],temp;
fstream info,tempfile;
info.open("playerinfo.dat",ios::binary|ios::in);
while(!info.eof())
{
info.read((char*)&s,sizeof(s));
if(info.eof())
break;
sc[i]=s;
i++;
}
nor=i;
for(i=0;i<(nor-1);i++)
{
min=sc[i].retscore();
loc=i;
for(j=i+1;j<nor;j++)
{
if(min<sc[j].retscore())
{
min=sc[j].retscore();
loc=j;
}
}
temp=sc[i];
sc[i]=sc[loc];
sc[loc]=temp;
}
info.close();
tempfile.open("temp.dat",ios::binary|ios::out);
for(i=0;i<nor;i++)
tempfile.write((char*)&sc[i],sizeof(scores));
tempfile.close();
remove("playerinfo.dat");
rename("temp.dat","playerinfo.dat");
info.open("playerinfo.dat",ios::binary|ios::in);
while(!info.eof())
{
info.read((char*)&s,sizeof(s));
if(info.eof())
break;
s.showinfo();
}
getch();
info.close();
break;
}
case 3:
{

clrscr();
textcolor(RED);cprintf("You are Deleting your record\n");
ifstream ifile("playerinfo.dat",ios::binary|ios::in);
ofstream ofile("temp.dat",ios::binary|ios::out);
char temp[20];
cout<<"\nEnter name: ";
gets(temp);
int found=-1;
while(!ifile.eof())
{
ifile.read((char*)&s,sizeof(s));
if(ifile.eof())
break;
if(s.retname(temp)==-1)
ofile.write((char*)&s,sizeof(s));
else
found=0;
}
ifile.close();
ofile.close();
remove("playerinfo.dat");
rename("temp.dat","playerinfo.dat");
if(found==-1)
cout<<"not found";
else
cout<<"\nRecord Deleted";
getch();
break;
}
case 4:
{
clrscr();
textcolor(RED);cprintf("You are Modifying your record\n");
int i=0,found=-1;
char temp[20];
info.open("playerinfo.dat",ios::binary|ios::in|ios::out);
cout<<"\nEnter name: ";
gets(temp);
while(!info.eof())
{
if(info.eof())
break;
info.read((char*)&s,sizeof(s));
i++;
if(s.retname(temp)==1)
{
cout<<"Enter new name: ";
s.modifyname();
info.seekg((i-1)*sizeof(s),ios::beg);
info.write((char*)&s,sizeof(s));
found=1;
}
}
if(found==-1)
cout<<"not found";
else
cout<<"\nRecord Modified";
info.close();
getch();
break;
}
case 5:
{
exit(0);
}
}
}while(opt<=5);
getch();
}
