/*******************************************************************************
* Copyright (c) 2011, 2014 UT-Battelle, LLC.
* All rights reserved. This program and the accompanying materials
* are made available under the terms of the Eclipse Public License v1.0
* which accompanies this distribution, and is available at
* http://www.eclipse.org/legal/epl-v10.html
*
* Contributors:
*   Initial API and implementation and/or initial documentation - Jay Jay Billings,
*   Jordan H. Deyton, Dasha Gorin, Alexander J. McCaskey, Taylor Patterson,
*   Claire Saunders, Matthew Wang, Anna Wojtowicz
*******************************************************************************/
package gov.lbnl.visit.swt;

import java.awt.Container;
import java.awt.GridBagConstraints;
import java.awt.GridBagLayout;
import java.awt.Insets;

import javax.swing.JLabel;
import javax.swing.JOptionPane;
import javax.swing.JPanel;
import javax.swing.JPasswordField;
import javax.swing.JTextField;

import com.jcraft.jsch.UIKeyboardInteractive;
import com.jcraft.jsch.UserInfo;

/**
 * @author hari
 *
 */
public class VisItRemoteConnectionUserInfoDialog implements UserInfo, UIKeyboardInteractive
{
	public boolean promptYesNo(String str) 
	{
		Object[] options = { "yes", "no" };
		int foo = JOptionPane.showOptionDialog(null, str, "Warning",
				JOptionPane.DEFAULT_OPTION, JOptionPane.WARNING_MESSAGE,
				null, options, options[0]);
		return foo == 0;
	}

	/** the passphrase typed in by the user */
	private String passphrase;

	/*
	 * returns the passphrase typed in by the user.
	 */
	public String getPassphrase() {
		return passphrase;
	}

	/*
	 * asks a key passphrase from the user.
	 */
	public boolean promptPassphrase(String message) {
		this.passphrase = promptPassImpl(message);
		return passphrase != null;
	}

	/** the password typed in by the user. */
	private String passwd;

	/**
	 * returns the password typed in by the user.
	 */
	public String getPassword() {
		return passwd;
	}

	/*
	 * asks a server password from the user.
	 */
	public boolean promptPassword(String message) {
		this.passwd = promptPassImpl(message);
		return passwd != null;
	}

	/**
	 * the common implementation of both {@link #promptPassword} and
	 * {@link #promptPassphrase}.
	 * 
	 * @return the string typed in, if the user confirmed, else {@code null}
	 *         .
	 */
	private String promptPassImpl(String message) {
		JTextField passwordField = new JPasswordField(20);
		Object[] ob = { passwordField };
		int result = JOptionPane.showConfirmDialog(null, ob, message,
				JOptionPane.OK_CANCEL_OPTION);
		if (result == JOptionPane.OK_OPTION) {
			String pwd = passwordField.getText();
			passwordField.setText("");
			return pwd;
		} else {
			passwordField.setText("");
			return null;
		}
	}

	/*
	 * shows a message to the user.
	 */
	public void showMessage(String message) {
		JOptionPane.showMessageDialog(null, message);
	}

	private final GridBagConstraints gbc = new GridBagConstraints(0, 0, 1,
			1, 1, 1, GridBagConstraints.NORTHWEST, GridBagConstraints.NONE,
			new Insets(0, 0, 0, 0), 0, 0);
	private Container panel;

	/*
	 * prompts the user a series of questions.
	 */
	public String[] promptKeyboardInteractive(String destination,
			String name, String instruction, String[] prompt, boolean[] echo) {
		panel = new JPanel();
		panel.setLayout(new GridBagLayout());

		gbc.weightx = 1.0;
		gbc.gridwidth = GridBagConstraints.REMAINDER;
		gbc.gridx = 0;
		panel.add(new JLabel(instruction), gbc);
		gbc.gridy++;

		gbc.gridwidth = GridBagConstraints.RELATIVE;

		JTextField[] texts = new JTextField[prompt.length];
		for (int i = 0; i < prompt.length; i++) {
			gbc.fill = GridBagConstraints.NONE;
			gbc.gridx = 0;
			gbc.weightx = 1;
			panel.add(new JLabel(prompt[i]), gbc);

			gbc.gridx = 1;
			gbc.fill = GridBagConstraints.HORIZONTAL;
			gbc.weighty = 1;
			if (echo[i]) {
				texts[i] = new JTextField(20);
			} else {
				texts[i] = new JPasswordField(20);
			}
			panel.add(texts[i], gbc);
			gbc.gridy++;
		}

		if (JOptionPane.showConfirmDialog(null, panel, destination + ": "
				+ name, JOptionPane.OK_CANCEL_OPTION,
				JOptionPane.QUESTION_MESSAGE) == JOptionPane.OK_OPTION) {
			String[] response = new String[prompt.length];
			for (int i = 0; i < prompt.length; i++) {
				response[i] = texts[i].getText();
			}
			return response;
		} else {
			return null; // cancel
		}
	}
}
