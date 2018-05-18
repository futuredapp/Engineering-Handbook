# Table views and Collection views

To simplify table view and collection view data source handling, we introduced a simple abstraction over those delegates. 

First part is a **cell model**, which can be seen as a model of a cell. Cell model is not reused and represents an item in the table/collection view. Cell models have all the data cell needs to display. The pairing between `UITableViewCell` or `UICollectionViewCell` and cell model is somewhat similar to the relation between view controller and view model. View model handles the data structure (usually array of arrays when sections are used too) containing the models.

Also, an auxiliary object is used as a delegate and data source, so no responsibilities are added to the view controller, the object is called `CellModelDataSource`. When the models are updated in the view model, it notifies the controller and table view the changes and sends the newly created or updated cell models for display.

Cell models implement the `CellModel/CellConvertible` protocol and the `UITableViewCell/UICollectionViewCell` sub-classes implement `CellConfigurable` protocol. When the cell is reused it is configured with according cell model.

![Table/collection view data source diagram](attachments/Figure.002.png)