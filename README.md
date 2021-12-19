# SpreadSheet
![online-spreadsheet-database-software-283593-666x418](https://user-images.githubusercontent.com/93831197/146638536-10d04c22-31b2-420c-bda7-c67a81b42d80.png)


**<h2>Introduction</h2>**

Spreadsheets are computer applications used to store, analyze, organize and manipulate data in the rows and columns of a grid. The program operates by taking in data, which can be numbers or text, into the cells of tables.  If the data is numbers, the program will compute it for you depending on the function you need to be completed.

In our project (Spreadsheet) the first part in it we ve focused on creating the menu bar and implementing the actions but in in this tp we will approach the second part and we will work on new  methods which are  **Go Cell** , **Find Location** and **Saving and loading Files**:

**<h2>1-Go Cell</h2>**

![Screenshot_133](https://user-images.githubusercontent.com/93831197/146657359-4016ffa7-5937-40c3-b966-99d917424484.png)


The purpose of this method is to give the location so that you can find the Cell 

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


This method is the reverse of Go Cell function and it consists of a dialog that  prompts the user for a input and seek a cell that contains the entered text.

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
**<h2>3-Saving Files</h2>**

![Screenshot_131](https://user-images.githubusercontent.com/93831197/146657457-7b9fffc0-c7d3-4cfe-9e18-a7ecdf32910b.png)


**Saving Content**

This private function is saving the content of our Spread Sheet in a simple format which stores the coordinate and the content of non empty cells .

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

In this operational function we will create a slot to respond to the action trigger in the header .

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

       
        recentFilesList.append(new QAction(filename,this));
        recentFilesMenu->addAction(recentFilesList[i]);

        connect(recentFilesList[i],&QAction::triggered,this,&SpreadSheet::openRecent);
        i++;
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

This private function is opening the content of our Spread Sheet in a simple format which open the coordinate file that we have just used .

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

In this function we will create a slot to respond to the action trigger in the header .\

Here is the code:
```javascript
void SpreadSheet::loadFileSlot(){

        QFileDialog d;
        auto filename = d.getOpenFileName();

        //ajouter le fichier qu'on vient d'ouvrir a recentFiles et creer la connexion
        recentFilesList.append(new QAction(filename,this));
        recentFilesMenu->addAction(recentFilesList[i]);

        connect(recentFilesList[i],&QAction::triggered,this,&SpreadSheet::openRecent);
        i++;
        if(filemenu)

       {
            currentFile = new QString(filename);
            setWindowTitle(*currentFile);

           openContent(*currentFile);

        }

}
```
**<h2>3-Saving Files (CSV)</h2>**


**Saving CSV Content**

This private function is saving the content of our Spread Sheet in a CSV format which stores the coordinate and the content of non empty cells .

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

In this operational function we will create a slot to respond to the action trigger in the header .

Here is the code:
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
**Load  CSV Files**

This private function is opening the content of our Spread Sheet in a CSV format which open the coordinate file that we have just used .

Here is the code :
```javascript
void SpreadSheet::loadCsvFileSlot(){

        QFileDialog d;
        auto filename = d.getOpenFileName();

        
        recentFilesList.append(new QAction(filename,this));
        recentFilesMenu->addAction(recentFilesList[i]);

        connect(recentFilesList[i],&QAction::triggered,this,&SpreadSheet::openRecent);
        i++;
        if(filemenu)

       {
            currentFile = new QString(filename);
            setWindowTitle(*currentFile);

           openCsvContent(*currentFile);

        }

}
```
**Recent Files**
This function the files that we have just using it recently.

To implement Open Recent we need to introduce the following objects and functions:

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


# Text Editor

**<h2>Introduction</h2>**

A text editor is a tool that allows a user to create and revise documents in a computer. Though this task can be carried out in other modes, the word text editor commonly refers to the tool that does this interactively. Earlier computer documents used to be primarily plain text documents, but nowadays due to improved input-output mechanisms and file formats, a document frequently contains pictures along with texts whose appearance (script, size, colour and style) can be varied within the document. Apart from producing output of such wide variety, text editors today provide many advanced features of interactiveness and output.

In this project we create a menu bar and toolbar with qt designer:


and the actions we code it
