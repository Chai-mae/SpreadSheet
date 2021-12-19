**<h2>Table of contents</h2>**

[Part 1: Spread Sheet](#Part-1:-SpreadSheet)

   [Introduction](#Introduction)
   
   [Go Cell](1-Go-Cell)
   
   [Find Dialog](#2-Find-Dialog)
   
   [Saving and Loading Files](#3-Saving-and-Loading-Files)
   
   [Recent Files](#Recent-Files)
   
 [Part 2: Text Editor](#Part-2:-Text-Editor)
   
   [Introduction](#Introduction)
   
   [Functionality](#Functionality)
   
   

#Part 1: SpreadSheet

![online-spreadsheet-database-software-283593-666x418](https://user-images.githubusercontent.com/93831197/146638536-10d04c22-31b2-420c-bda7-c67a81b42d80.png)


**<h2>Introduction</h2>**

* Spreadsheets are computer applications used to store, analyze, organize and manipulate data in the rows and columns of a grid. The program operates by taking in data, which can be numbers or text, into the cells of tables.  If the data is numbers, the program will compute it for you depending on the function you need to be completed.

* In our project (Spreadsheet) the first part in it we've focused on creating the menu bar and implementing the actions but in this homework we will approach the second part and we will work on new  methods which are  **Go Cell** , **Find Location** and **Saving and loading Files**.

![Screenshot_130](https://user-images.githubusercontent.com/93831197/146657494-ef1fdac3-70ee-447f-8cc7-1b4584dc8d66.png)




**<h2>1-Go Cell</h2>**

![Screenshot_133](https://user-images.githubusercontent.com/93831197/146657359-4016ffa7-5937-40c3-b966-99d917424484.png)


* The purpose of this method is to give the location so that you can find the Cell 

For that ,we create a dialog for the user to select a cell and here is the code:
 ```javascript
 void SpreadSheet::goCellSlot(){
    // creer un dialog
    GoDialog d;
     //executer le dialog
    auto reply=d.exec();

    //verifier si le dialog a ete acceplte
    if(reply == GoDialog::Accepted)
    {


        // extraire le text
        QString cell = d.getCell();
        //extraire la ligne

        int row =  cell[0].toLatin1()- 'A';

        //Extraire la colonne

        cell = cell.remove(0,1);
        int col = cell.toInt()-1;

        // changer la cellule
        statusBar()->showMessage("Changing the current cell" , 2000);
        spreadsheet->setCurrentCell(row, col);


     }

}
```
**<h2>2-Find Dialog</h2>**

![Screenshot_134](https://user-images.githubusercontent.com/93831197/146657388-20694252-21a1-4d1b-89b4-2e4e705789d2.png)


* This method is the reverse of Go Cell function and it consists of a dialog that  prompts the user for a input and seek a cell that contains the entered text.

Here is the code :
```javascript
void SpreadSheet::findSlot(){
    FindDialog dialog;
     auto reply= dialog.exec();
     auto found= false;

     if(reply == FindDialog::Accepted)
     {
         QString text= dialog.getText();
         for(auto i=0; i<spreadsheet->rowCount(); i++)
         {
             for(auto j=0; j<spreadsheet->columnCount(); j++)
             {
                 if(spreadsheet->item(i,j))
                 {
                   QString searchword = spreadsheet->item(i,j)->text();
                    if(searchword.contains(text))
                    {
                        spreadsheet->setCurrentCell(i,j);
                        found= true;



                    }


                }
}
         }
         if(!found)
               QMessageBox::information(this, "Error", "No such word or expression");


     }
     }
```
**<h2>3-Saving and Loading Files</h2>**
* In this part we will implement several methods and slots to save and open files in two different format which are :
   - the simple format (coordinate format)
   - CSV format

**Simple format**

![Screenshot_131](https://user-images.githubusercontent.com/93831197/146657457-7b9fffc0-c7d3-4cfe-9e18-a7ecdf32910b.png)


**Saving Content**

* This private function is saving the content of our Spread Sheet in a simple format which stores the coordinate and the content of non empty cells .

Here is the code :
```javascript
void SpreadSheet::saveContent(QString filename) {

  
    //1 pointeur sur le fichier d interet
    QFile file(filename);

    //2 ouvrir le fichier en mode write
    if(file.open(QIODevice::WriteOnly))
    {
        QTextStream out(&file);

         //parcourir les cellules et sauvegarder leur contenu
        for (int i=0;i<spreadsheet->rowCount() ;i++ ) {
            for(int j=0; j<spreadsheet->columnCount();j++){
                auto cell = spreadsheet->item(i,j);
                if(cell)
                    out << i<< "," << j << "," << cell->text() << Qt::endl;
            }

        }
    }



    // fermer le fichier
    file.close();



}
```
**Save File action**

* In this operational function we will create a slot to respond to the action trigger in the header .

Here is the code:
```javascript
void SpreadSheet::saveSlot(){
    // tester si on possede un nom de fichier
    if(!currentFile)
    {
        //creer Factory design
        QFileDialog d;

        // creer un dialog pour obtenir le nom de fichier
        auto filename = d.getSaveFileName();

       
        // changer le nom du fichier courant
        currentFile = new QString(filename);
        // mettre a jour le titre de le fenetre
        setWindowTitle(*currentFile);

    }
    // sauvegarder le contenu
    saveContent(*currentFile);


}
```
**Load Files**

* This private function is opening the content of our Spread Sheet in a simple format which open the coordinate file that we have stored previously .

Here is the code :
```javascript
void SpreadSheet::openContent(QString filename) {
    //open coordinate file
    // obtenir le fichier
    QFile file(filename);
    QString line;
     if(file.open(QIODevice::ReadOnly))
     {
         QTextStream in(&file);
         while( !in.atEnd())
         {
             line = in.readLine();
             auto tokens = line.split(QChar(',') );
             int row = tokens[0].toInt();
             int col = tokens[1].toInt();
             spreadsheet->setItem(row, col , new QTableWidgetItem(tokens[2]));
         }


     }

     file.close();
}
```

**Load File action**

* In this function we will create a slot to respond to the action trigger in the header .

Here is the code:
```javascript
void SpreadSheet::loadFileSlot(){

        QFileDialog d;
        auto filename = d.getOpenFileName();
        
        if(filemenu)

       {
            currentFile = new QString(filename);
            setWindowTitle(*currentFile);

           openContent(*currentFile);

        }

}
```
**CSV format**

![Screenshot_139 (1)](https://user-images.githubusercontent.com/93831197/146677035-9e3a37bc-3020-434b-bcab-e1da255d4982.png)


**Saving CSV Content**

* This private function is saving the content of our Spread Sheet in a CSV format which stores both empty and  non empty cells .

Here is the code :
```javascript
void SpreadSheet::saveascsvContent(QString filename) {
    QFile file(filename);

   int rows = spreadsheet->rowCount();
   int columns = spreadsheet->columnCount();
   if(file.open(QIODevice::WriteOnly )) {

       QTextStream out(&file);


   for (int i = 0; i < rows; i++) {
       for (int j = 0; j < columns; j++) {

           auto cell = spreadsheet->item(i,j);
           if(cell)
               out<< cell->text()<<",";
           else
               out<< " "<<",";

       }
        out<< Qt::endl;

   }


   file.close();
}
}
```
**Save CSV File action**

* In this operational function we will create a slot to respond to the action trigger in the header .

Here is the code:
```javascript
void SpreadSheet::loadCsvFileSlot(){

        QFileDialog d;
        auto filename = d.getOpenFileName();

        if(filemenu)

       {
            currentFile = new QString(filename);
            setWindowTitle(*currentFile);

           openCsvContent(*currentFile);

        }

}
```
**Load  CSV Files**

* This private function is opening the content of a CSV file that we have stored before .

Here is the code :
```javascript
void SpreadSheet::openCsvContent(QString filename){
         //open csv file
         QFile file(filename);
         QStringList list;
         int count = 0;
          if(file.open(QIODevice::ReadOnly))
          {
              QTextStream in(&file);

              while( !in.atEnd())
              {
                  QString line = in.readLine();
                  for(auto content : line.split(","))
                      list.append(content);
                  for(auto i =0; i<list.length(); i++)
                      spreadsheet->setItem(count,i, new QTableWidgetItem(list[i]));
                 count++;
                 list.clear();
              }


          }
         file.close();

}
```
**Load File action**
* In this operational function we will create a slot to respond to the action trigger in the header .
```javascript
void SpreadSheet::loadCsvFileSlot(){

        QFileDialog d;
        auto filename = d.getOpenFileName();

        if(filemenu)

       {
            currentFile = new QString(filename);
            setWindowTitle(*currentFile);

           openCsvContent(*currentFile);

        }

}
```

**Recent Files**
* This function shows the files that we have used recently.

* To implement Open Recent we need to introduce the following objects and functions:
   - a list of QActions QList<QAction*> recentFileActionList. This list of actions represents the recently opened files
   - a submenu QMenu* recentFilesMenu which will appear after we click on Open Recent
   - a slot openRecent() that is called anytime we choose a file from recentFilesMenu
**Implementation**
* Each time we open or save a file its path appears in the open recent subMenu.
We add the following code to the save and load slots :
```javascript
 recentFilesList.append(new QAction(filename,this));
        recentFilesMenu->addAction(recentFilesList[i]);

        connect(recentFilesList[i],&QAction::triggered,this,&SpreadSheet::openRecent);
        i++;
```
* Now to open the files in the open recent subMenu we call the openRecent() slot :
```javascript
void SpreadSheet::openRecent(){
    QAction *action = qobject_cast<QAction *>(sender());
    if (action)
    {
        openContent(action->text());
        saveContent(action->text());
        saveascsvContent(action->text());
    }

}
```


# Part 2: Text Editor

**<h2>Introduction</h2>**

* A text editor is a tool that allows a user to create and revise documents in a computer. Though this task can be carried out in other modes, the word text editor commonly refers to the tool that does this interactively. Earlier computer documents used to be primarily plain text documents, but nowadays due to improved input-output mechanisms and file formats, a document frequently contains pictures along with texts whose appearance (script, size, colour and style) can be varied within the document. Apart from producing output of such wide variety, text editors today provide many advanced features of interactiveness and output.

* In this project we create a text editor using the QPlainTextEditor library with Qt Designer for a rapid design of it features:

![3](https://user-images.githubusercontent.com/93831197/146680242-8fc37030-249b-41df-a6c3-f18892e41def.jpeg)

These are the set of menus of our application:

![4](https://user-images.githubusercontent.com/93831197/146680307-6483d989-efed-4ecc-9ba5-0772a67b9d6b.jpeg) ![5](https://user-images.githubusercontent.com/93831197/146680310-e1163976-3f6d-4664-adb5-c6fc282cca12.jpeg) ![6](https://user-images.githubusercontent.com/93831197/146680314-ce6bb667-d200-47aa-bbee-62777e8e766c.jpeg)



**Functionality** 

Here is the code the functionality of the Text Editor :

```javascript
void textEditor::on_action_Exit_triggered()
{
    ui->statusbar->showMessage("Quitting ...");

    QApplication::quit();

}


void textEditor::on_actionNew_triggered()
{

    currentFile.clear();
    currentFile=nullptr;
    ui->plainTextEdit->setPlainText(QString());
    ui->statusbar->showMessage("Creating a new file");


}


void textEditor::on_actionOpen_triggered()
{
    QString filename= QFileDialog::getOpenFileName(this,"Open the file");
    QFile file(filename);
    currentFile = filename;
    if(!file.open(QIODevice::ReadOnly | QFile::Text))
        QMessageBox::warning(this,"Warning","Cannot open file :"+file.errorString());
    setWindowTitle(filename);
    QTextStream in(&file);
    QString text = in.readAll();
    ui->plainTextEdit->setPlainText(text);
    file.close();
    ui->statusbar->showMessage("Opening the chosen file...");


}

void textEditor::on_action_Copy_triggered()
{
    ui->plainTextEdit->copy();
    ui->statusbar->showMessage("Copying the current selection");


}


void textEditor::on_action_Paste_triggered()
{
   ui->plainTextEdit->paste();
   ui->statusbar->showMessage("Pasting the previous copied selection");

}


void textEditor::on_action_Cut_triggered()
{
    ui->plainTextEdit->cut();
    ui->statusbar->showMessage("Cutting the current selection");

}





void textEditor::on_actionAbout_Qt_triggered()
{
    QMessageBox::aboutQt(this, "About Me");
    ui->statusbar->showMessage("About Qt...");


}


void textEditor::on_action_About_triggered()
{
    QMessageBox::about(this, tr("About Application"),
             tr("The <b>Application</b> example demonstrates how to "
                "write modern GUI applications using Qt, with a menu bar, "
                "toolbars, and a status bar."));
    ui->statusbar->showMessage("About ...");


}


bool textEditor::saveFile(const QString &filename){
    QString errorMessage;

    QGuiApplication::setOverrideCursor(Qt::WaitCursor);
    QSaveFile file(filename);
    if (file.open(QFile::WriteOnly)) {
        QTextStream out(&file);
        out << ui->plainTextEdit->toPlainText();
        if (!file.commit()) {
            errorMessage = tr("Cannot write file %1:\n%2.")
                           .arg(QDir::toNativeSeparators(filename), file.errorString());
        }
    } else {
        errorMessage = tr("Cannot open file %1 for writing:\n%2.")
                       .arg(QDir::toNativeSeparators(filename), file.errorString());
    }
    QApplication::restoreOverrideCursor();

    if (!errorMessage.isEmpty()) {
        QMessageBox::warning(this, tr("Application"), errorMessage);
        return false;
    }

    setCurrentFile(filename);
    statusBar()->showMessage(tr("File saved"), 2000);
    return true;
}
bool textEditor::Save()
{
    if (currentFile.isEmpty()) {
        return SaveAs();
    } else {
        return saveFile(currentFile);
    }
    ui->statusbar->showMessage("Saving the file ...");

}


bool textEditor::SaveAs()
{

    QFileDialog dialog(this);
    dialog.setWindowModality(Qt::WindowModal);
    dialog.setAcceptMode(QFileDialog::AcceptSave);
    if (dialog.exec() != QDialog::Accepted)
        return false;
    return saveFile(dialog.selectedFiles().first());
    ui->statusbar->showMessage("Saving the file as ...");


}
void textEditor::setCurrentFile(const QString &filename){
    currentFile = filename;
    ui->plainTextEdit->document()->setModified(false);
    setWindowModified(false);

    QString shownName = currentFile;
    if (currentFile.isEmpty())
        shownName = "untitled.txt";
    setWindowFilePath(shownName);
}
```

**<h2>Made by:</h2>**
<h3>Ikram Belmadani</h3>
<h3>Biyaye Chaimae</h3>
