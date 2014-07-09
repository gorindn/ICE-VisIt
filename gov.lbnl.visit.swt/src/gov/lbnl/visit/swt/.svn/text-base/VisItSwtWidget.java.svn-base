package gov.lbnl.visit.swt;

import gov.lbnl.visit.swt.VisItSwtConnection.VISIT_CONNECTION_TYPE;
import gov.lbnl.visit.swt.VisItSwtConnection.VisualizationUpdateCallback;

import java.io.ByteArrayInputStream;
import java.util.ArrayList;
import java.util.Vector;

import org.eclipse.swt.SWT;
import org.eclipse.swt.graphics.GC;
import org.eclipse.swt.graphics.Image;
import org.eclipse.swt.widgets.Canvas;
import org.eclipse.swt.widgets.Composite;
import org.eclipse.swt.widgets.Display;
import org.eclipse.swt.widgets.Event;
import org.eclipse.swt.widgets.Listener;

import com.google.gson.JsonArray;
import com.google.gson.JsonObject;

import visit.java.client.AttributeSubject;
import visit.java.client.AttributeSubject.AttributeSubjectCallback;
import visit.java.client.Transformation;
import visit.java.client.ViewerMethods;
import visit.java.client.ViewerState;


/**
 * This class extends SWT Canvas to provide an area to display images rendered
 * by an external VisIt client.
 * 
 * @authors hkq, tnp
 */
public class VisItSwtWidget extends Canvas implements Listener,
		AttributeSubjectCallback, VisualizationUpdateCallback {

	/**
	 * The database metadata.
	 */
	private FileInfo openDatabaseInfo;

	/**
	 * The composite to contain the canvas.
	 */
	private Composite shell;

	/**
	 * 
	 */
	private Image image;

	/**
	 * Connection to VisIt.
	 */
	private VisItSwtConnection visitConnection;
	
	/**
	 * windowId for this connection. 
	 */
	private int visitWindowId;
	
	/**
	 * The trackball used to control the VisIt image.
	 */
	Trackball ball = new Trackball();

	/**
	 * 
	 */
	private boolean initialized = false;

	/**
	 * The constructor
	 * 
	 * @param visComp
	 *            The parent composite for this Canvas.
	 * @param x
	 *            The SWT constant style to be applied to the Canvas.
	 */
	public VisItSwtWidget(Composite visComp, int x) {

		// Call Canvas' constructor
		super(visComp, x);

		// Get the Shell of the parent Composite
		shell = visComp.getShell();
		// shell.getDisplay();

		// Initialize the default image
		image = shell.getDisplay().getSystemImage(SWT.ICON_ERROR);

		// Register this as an SWT.Paint listener
		addListener(SWT.Paint, this);
	}
	
	/**
	 * setVisItSwtConnection
	 * 
	 * @param VisItSwtConnection
	 *            Connection
	 * @param windowId
	 *            The Window Id to draw.
	 */
	public void setVisItSwtConnection(VisItSwtConnection conn, int windowId, int windowWidth, int windowHeight) {
		visitConnection = conn;
		visitWindowId = windowId;
		
		ball.screenWidth = windowWidth;
		ball.screenHeight = windowHeight;
		ball.radius = ball.screenWidth / 2;

		visitConnection.registerVisualization(VISIT_CONNECTION_TYPE.IMAGE, visitWindowId, this);
		visitConnection.registerCallback("avtDatabaseMetaData", this);
		initialized = true;
		

	}

	public boolean hasInitialized() {
		return initialized;
	}
	
	/**
	 * This operation draws the image to the Canvas.
	 * 
	 * @param e
	 *            The Event triggering the need to redraw the Image in the
	 *            Canvas
	 */
	public void handleEvent(Event e) {

		GC gc = e.gc;
		gc.drawImage(image, 0, 0);

		return;
	}

	/**
	 * 
	 * @param arg0
	 */
	private void updateDatabaseMetaData(AttributeSubject arg0) {

		FileInfo fi = new FileInfo();

		String filename = arg0.get("databaseName").getAsString();
		String filetype = arg0.get("fileFormat").getAsString();
		String description = arg0.get("databaseComment").getAsString();
		
		ArrayList<String> fi_meshes = new ArrayList<String>();
		ArrayList<String> fi_scalars = new ArrayList<String>();
		ArrayList<String> fi_vectors = new ArrayList<String>();
		ArrayList<String> fi_materials = new ArrayList<String>();
		
		JsonArray meshes = arg0.get("meshes").getAsJsonArray();
		for (int i = 0; i < meshes.size(); ++i) {
			JsonObject mesh = meshes.get(i).getAsJsonObject();
			String name = arg0.getAttr(mesh, "name").getAsString();
			fi_meshes.add(name);
		}

		JsonArray scalars = arg0.get("scalars").getAsJsonArray();
		for (int i = 0; i < scalars.size(); ++i) {
			JsonObject scalar = scalars.get(i).getAsJsonObject();
			String name = arg0.getAttr(scalar, "name").getAsString();
			fi_scalars.add(name);
		}

		JsonArray vectors = arg0.get("vectors").getAsJsonArray();
		for (int i = 0; i < vectors.size(); ++i) {
			JsonObject vector = vectors.get(i).getAsJsonObject();
			String name = arg0.getAttr(vector, "name").getAsString();
			fi_vectors.add(name);
		}

		JsonArray materials = arg0.get("materials").getAsJsonArray();
		for (int i = 0; i < materials.size(); ++i) {
			JsonObject material = materials.get(i).getAsJsonObject();
			String name = arg0.getAttr(material, "name").getAsString();
			fi_materials.add(name);
		}
	
		fi.setFileName(filename);
		fi.setFileType(filetype);
		fi.setFileDescription(description);
		
		fi.setMeshes(fi_meshes);
		fi.setScalars(fi_scalars);
		fi.setVectors(fi_vectors);
		fi.setMaterials(fi_materials);
		
		openDatabaseInfo = fi;
	}

	/**
	 * 
	 */
	@Override
	synchronized public void update(AttributeSubject arg0) {
		// System.out.println(arg0.getData().toString());

		String typename = arg0.getTypename();

		if (typename.equals("avtDatabaseMetaData")) {
			updateDatabaseMetaData(arg0);
		}
	}

	
	/**
	 * 
	 * @return
	 */
	public ViewerMethods getViewerMethods() {
		return visitConnection.getViewerMethods(); //client.getViewerMethods();
	}

	/**
	 * 
	 * @return
	 */
	public ViewerState getViewerState() {
		return null; //client.getViewerState();
	}

//	/**
//	 * 
//	 */
//	public void invertBackground() {
//		if (!hasInitialized())
//			return;
//
//		ViewerMethods methods = client.getViewerMethods();
//		methods.InvertBackgroundColor();
//	}

	/**
	 * 
	 * @param windowId
	 * @param w
	 * @param h
	 */
//	public void resizeWindow(int windowId, int w, int h) {
//		if (!hasInitialized())
//			return;
//
//		ViewerMethods methods = client.getViewerMethods();
//		methods.ResizeWindow(windowId, w, h);
//	}

	/**
	 * 
	 */
//	public void openDatabase(String filename) {
//		if (!hasInitialized())
//			return;
//
//		ViewerMethods methods = client.getViewerMethods();
//		methods.OpenDatabase(filename);
//
//		// Get filename for the database..
//		methods.GetMetaData(filename);
//
//		sync();
//	}

	/**
	 * 
	 */
//	public void addPlot(String plot_name, String plot_var) {
//		if (!hasInitialized())
//			return;
//
//		ViewerMethods methods = client.getViewerMethods();
//		methods.AddPlot(plot_name, plot_var);
//	}

	/**
	 * 
	 */
//	public void drawPlots() {
//		if (!hasInitialized())
//			return;
//
//		ViewerMethods methods = client.getViewerMethods();
//		methods.DrawPlots();
//	}

	/**
	 * 
	 */
//	public void hideShowPlot(int index) {
//		if (!hasInitialized())
//			return;
//
//		ViewerMethods methods = client.getViewerMethods();
//		methods.SetActivePlots(index);
//		methods.HideActivePlots();
//
//		ball.resetModelMatrices();
//	}

	/**
	 * 
	 */
//	public void deletePlots() {
//		if (!hasInitialized())
//			return;
//
//		ViewerMethods methods = client.getViewerMethods();
//		methods.DeleteActivePlots();
//
//		ball.resetModelMatrices();
//	};

	/**
	 * 
	 */
//	public void resetView() {
//		if (!hasInitialized())
//			return;
//
//		ViewerMethods methods = client.getViewerMethods();
//		methods.ResetView();
//
//		ball.resetModelMatrices();
//	}

	/**
	 * 
	 * @return
	 */
	public FileInfo getFileInfo() {
		return openDatabaseInfo;
	}

	/**
	 * 
	 */
//	private void sync() {
//		openDatabaseInfo = null;
//		try {
//			getViewerMethods().Synchronize();
//		} catch (Exception e) {
//			// TODO Auto-generated catch block
//			e.printStackTrace();
//		}
//	}

	/**
	 * 
	 * @param direction
	 */
	public void zoom(String direction) {
		if (direction == "in")
			ball.wheelIn();
		if (direction == "out")
			ball.wheelOut();
	}

	/**
	 * 
	 * @param x
	 * @param y
	 */
	public void mouseStart(int x, int y) {
		ball.moveStart(x, y);

		// Transformation.VectorN newViewUp = ball.modelMat.get(1);
		// Transformation.VectorN newViewNormal = ball.modelMat.get(2);
		//
		// Vector<Double> up = new Vector<Double>();
		// Vector<Double> normal = new Vector<Double>();
		//
		// for (int i = 0; i < 3; ++i) {
		// up.add(newViewUp.get(i));
		// normal.add(newViewNormal.get(i));
		// }
		//
		// client.getViewerMethods().UpdateView(up, normal);
	}

	/**
	 * 
	 * @param x
	 * @param y
	 */
	public void mouseMove(int x, int y) {
		ball.move(x, y);
	}

	/**
	 * 
	 * @param x
	 * @param y
	 */
	public void mouseStop(int x, int y) {
		ball.moveStop(x, y);

		//System.out.println(ball.modelMat);
		Transformation.VectorN newViewUp = ball.modelMat.get(1);
		Transformation.VectorN newViewNormal = ball.modelMat.get(2);

		Vector<Double> up = new Vector<Double>();
		Vector<Double> normal = new Vector<Double>();

		for (int i = 0; i < 3; ++i) {
			up.add(newViewUp.get(i));
			normal.add(newViewNormal.get(i));
		}

		getViewerMethods().updateView(up, normal);
	}

	@Override
	public void update(VISIT_CONNECTION_TYPE type, byte[] rawData) {
		
		if(type != VISIT_CONNECTION_TYPE.IMAGE) {
			return;
		}
		
		final byte[] output = rawData;
		// TODO Auto-generated method stub
		Display.getDefault().asyncExec(new Runnable() {
			public void run() {
				ByteArrayInputStream bis = new ByteArrayInputStream(
						output);
				image = new Image(shell.getDisplay(), bis);
				redraw();
			}
		});
	}
}
