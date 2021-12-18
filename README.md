# SpreadSheet
![online-spreadsheet-database-software-283593-666x418](https://user-images.githubusercontent.com/93831197/146638536-10d04c22-31b2-420c-bda7-c67a81b42d80.png)


**<h2>Introduction</h2>**

Spreadsheets are computer applications used to store, analyze, organize and manipulate data in the rows and columns of a grid. The program operates by taking in data, which can be numbers or text, into the cells of tables.  If the data is numbers, the program will compute it for you depending on the function you need to be completed.

In our project (Spreadsheet) the first part in it we ve focused on creating the menu bar and implementing the actions but in in this tp we will approach the second part and we will work on new  methods which are  **Go Cell** , **Find Location** and **Saving Files**:

**<h2>1-Go Cell</h2>**

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
