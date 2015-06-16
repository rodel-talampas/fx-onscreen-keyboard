# Introduction #

## Run the demo ##
To test the project you need include the jar file of the last snapshot **or** use the jar included demo code at:

FX Demo:

http://code.google.com/p/fx-onscreen-keyboard/source/browse/fx-onscreen-keyboard/src/main/java/org/comtel/javafx/MainDemo.java

Swing Demo:

http://code.google.com/p/fx-onscreen-keyboard/source/browse/fx-onscreen-keyboard/src/main/java/org/comtel/javafx/SwingMainDemo.java

### JavaFXKeyboardTest.java ###

```
public class JavaFXKeyboardTest extends Application {

	static KeyBoardPopup popup;

	@Override
	public void start(Stage stage) throws Exception {
		stage.setTitle("FX Keyboard (" + System.getProperty("javafx.runtime.version") + ")");

		Parent root = FXMLLoader.load(getClass().getResource("TestArea.fxml"));
		Scene scene = new Scene(root);

		// add keyboard scene listener to all text components
		scene.focusOwnerProperty().addListener(new ChangeListener<Node>() {
			@Override
			public void changed(ObservableValue<? extends Node> value, Node n1, Node n2) {
				if (n2 != null && n2 instanceof TextInputControl) {
					setPopupVisible(true, (TextInputControl) n2);
				} else {
					setPopupVisible(false, null);
				}
			}
		});

		popup = KeyBoardPopupBuilder.create().initScale(1.2)
				// (optional) path to your keyboard layouts
				//.layerPath(Paths.get("xml", ""))
				// choose your prefered startup locale
				.initLocale(Locale.ENGLISH)
				// choose swing or javafx keyboard controller
				.addIRobot(RobotFactory.createFXRobot())
				.build();
		
		popup.getKeyBoard().setOnKeyboardCloseButton(new EventHandler<Event>() {
			public void handle(Event event) {
				setPopupVisible(false, null);
			}
		});

		//set default jar contained css layout
		String css = this.getClass().getResource("/css/KeyboardButtonStyle.css").toExternalForm();
		System.err.println(css);
		scene.getStylesheets().add(css);
		popup.setAutoFix(false);
		popup.show(stage);

		stage.setScene(scene);
		stage.show();

	}

	static void setPopupVisible(final boolean b, final TextInputControl textNode) {

		Platform.runLater(new Runnable() {

			@Override
			public void run() {
				if (b) {
					if (textNode != null) {
						Rectangle2D textNodeBounds = new Rectangle2D(textNode.getScene().getWindow().getX()
								+ textNode.getLocalToSceneTransform().getTx(), textNode.getScene().getWindow().getY()
								+ textNode.getLocalToSceneTransform().getTy(), textNode.getWidth(), textNode
								.getHeight());
						
						System.out.println(textNodeBounds);
						Rectangle2D screenBounds = Screen.getPrimary().getVisualBounds();
						System.err.println( popup.getWidth());
						if (textNodeBounds.getMinX() + popup.getWidth() > screenBounds.getMaxX()){
							popup.setX(screenBounds.getMaxX() - popup.getWidth());
						}else{
							popup.setX(textNodeBounds.getMinX());
						}
						
						if (textNodeBounds.getMaxY() + popup.getHeight() > screenBounds.getMaxY()){
							popup.setY(textNodeBounds.getMinY() - popup.getHeight() + 20);
						}else{
							popup.setY(textNodeBounds.getMaxY() + 40);
						}
					}
					if (!popup.isShowing()) {
						popup.show(popup.getOwnerWindow());
					}
				} else {
					popup.hide();
				}

			}
		});

	}

	public static void main(String[] args) {
		launch(args);
	}

}
```

### TestAreaController.java ###

```
public class TestAreaController implements Initializable {

	@FXML
	private Label label;

	@FXML
	private TableView<Person> table;

	@FXML
	private TableColumn<Object, String> nameColumn;
	
	@FXML
	private void openKeyboard() {
		JavaFXKeyboardTest.setPopupVisible(true, null);
	}

	@FXML
	private void closeKeyboard() {
		JavaFXKeyboardTest.setPopupVisible(false, null);
	}

	@Override
	public void initialize(URL url, ResourceBundle rb) {
		table.setEditable(true);
		table.setItems(data);
		nameColumn.setCellValueFactory(new PropertyValueFactory<Object, String>("firstName"));
		nameColumn.setCellFactory(TextFieldTableCell.forTableColumn());
	}

	final ObservableList<Person> data = FXCollections.observableArrayList(new Person("Jacob", "Smith",
			"jacob.smith@example.com"), new Person("Isabella", "Johnson", "isabella.johnson@example.com"), new Person(
			"Ethan", "Williams", "ethan.williams@example.com"), new Person("Emma", "Jones", "emma.jones@example.com"),
			new Person("Michael", "Brown", "michael.brown@example.com"));

	public class Person {
		private final SimpleStringProperty firstName;
		private final SimpleStringProperty lastName;
		private final SimpleStringProperty email;

		private Person(String fName, String lName, String email) {
			this.firstName = new SimpleStringProperty(fName);
			this.lastName = new SimpleStringProperty(lName);
			this.email = new SimpleStringProperty(email);
		}

		public String getFirstName() {return firstName.get();}
		public void setFirstName(String fName) {firstName.set(fName);}
		public String getLastName() {return lastName.get();}
		public void setLastName(String fName) {lastName.set(fName);}
		public String getEmail() {return email.get();}
		public void setEmail(String fName) {email.set(fName);}

	}
}


```
### TestArea.fxml ###

```
<?xml version="1.0" encoding="UTF-8"?>

<?import java.lang.*?>
<?import java.util.*?>
<?import javafx.scene.*?>
<?import javafx.scene.control.*?>
<?import javafx.scene.layout.*?>

<AnchorPane id="AnchorPane" prefHeight="386.0" prefWidth="490.0" xmlns:fx="http://javafx.com/fxml" fx:controller="javafxkeyboardtest.TestAreaController">
  <children>
    <SplitPane dividerPositions="0.4672131147540984" focusTraversable="true" prefHeight="386.0" prefWidth="490.0" AnchorPane.bottomAnchor="0.0" AnchorPane.leftAnchor="0.0" AnchorPane.rightAnchor="0.0" AnchorPane.topAnchor="0.0">
      <items>
        <AnchorPane minHeight="0.0" minWidth="0.0" prefHeight="384.0" prefWidth="301.0">
          <children>
            <Label layoutX="14.0" layoutY="65.0" text="Label" />
            <TextField fx:id="tf" layoutX="11.0" layoutY="30.0" prefWidth="200.0" promptText="Demo" text="TextField" />
            <Label layoutX="14.0" layoutY="14.0" text="Label" />
            <TextArea fx:id="ta" layoutX="14.0" layoutY="88.0" prefHeight="69.0" prefWidth="200.0" text="TextArea" wrapText="true" />
            <Button fx:id="button" layoutX="105.0" layoutY="171.0" text="Close Keyboard!" />
            <TitledPane animated="false" layoutY="227.0" minWidth="218.0" prefHeight="157.0" prefWidth="225.0" text="untitled">
              <content>
                <Pane prefHeight="32.0" prefWidth="320.0">
                  <children>
                    <TextField id="tf" layoutX="14.0" layoutY="14.0" prefHeight="22.0" prefWidth="200.0" promptText="No Keyboard" text="TextField in Pane" />
                  </children>
                </Pane>
              </content>
            </TitledPane>
          </children>
        </AnchorPane>
        <AnchorPane minHeight="0.0" minWidth="0.0" prefHeight="384.0" prefWidth="200.0">
          <children>
            <AnchorPane id="Content" layoutX="2.0" minHeight="0.0" minWidth="0.0" prefHeight="131.0" prefWidth="257.0">
              <children>
                <TextField id="tf" layoutX="14.0" layoutY="14.0" prefHeight="22.0" prefWidth="200.0" promptText="No Keyboard" text="TextField in Pane" />
                <TextArea fx:id="ta2" layoutX="14.0" layoutY="46.0" prefHeight="56.0" prefWidth="200.0" text="TextArea" wrapText="true" />
              </children>
            </AnchorPane>
            <TableView fx:id="table" editable="true" layoutX="2.0" layoutY="131.0" prefHeight="253.0" prefWidth="264.0" tableMenuButtonVisible="true">
              <columns>
                <TableColumn prefWidth="75.0" text="Name" fx:id="nameColumn" />
                <TableColumn prefWidth="75.0" text="B" />
                <TableColumn prefWidth="75.0" text="C" />
              </columns>
            </TableView>
          </children>
        </AnchorPane>
      </items>
    </SplitPane>
  </children>
</AnchorPane>
```