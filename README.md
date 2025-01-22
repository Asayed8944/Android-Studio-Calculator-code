package com.example.firstapp;
import android.os.Bundle;
import android.text.TextUtils;
import android.widget.ArrayAdapter;
import android.widget.EditText;
import android.widget.ListView;
import android.widget.Toast;
import androidx.appcompat.app.AlertDialog;
import androidx.appcompat.app.AppCompatActivity;

import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {

    private EditText editTextTask;
    private ArrayList<String> taskList;
    private ArrayAdapter<String> adapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Initialize UI elements
        editTextTask = findViewById(R.id.editTextTask);
        ListView listViewTasks = findViewById(R.id.listViewTasks);

        // Initialize the task list and adapter
        taskList = new ArrayList<>();
        adapter = new ArrayAdapter<>(this, android.R.layout.simple_list_item_1, taskList);
        listViewTasks.setAdapter(adapter);

        // Add task when the Add button is clicked
        findViewById(R.id.btnAdd).setOnClickListener(v -> addTask());

        // Handle click on task for editing or deleting
        listViewTasks.setOnItemClickListener((parent, view, position, id) -> {
            String selectedTask = taskList.get(position);
            showEditDeleteDialog(selectedTask, position);
        });
    }

    // Adds a new task to the list
    private void addTask() {
        String task = editTextTask.getText().toString().trim();
        if (TextUtils.isEmpty(task)) {
            Toast.makeText(this, "Task cannot be empty", Toast.LENGTH_SHORT).show();
            return;
        }
        taskList.add(task);
        adapter.notifyDataSetChanged(); // Refresh the ListView
        editTextTask.setText(""); // Clear the input field
    }

    // Shows a dialog to edit or delete the selected task
    private void showEditDeleteDialog(String task, int position) {
        final EditText editTaskInput = new EditText(this);
        editTaskInput.setText(task);

        new AlertDialog.Builder(this)
                .setTitle("Edit or Delete Task")
                .setView(editTaskInput)
                .setPositiveButton("Update", (dialog, which) -> {
                    String updatedTask = editTaskInput.getText().toString().trim();
                    if (!TextUtils.isEmpty(updatedTask)) {
                        taskList.set(position, updatedTask);
                        adapter.notifyDataSetChanged();
                    } else {
                        Toast.makeText(this, "Task cannot be empty", Toast.LENGTH_SHORT).show();
                    }
                })
                .setNegativeButton("Delete", (dialog, which) -> {
                    taskList.remove(position);
                    adapter.notifyDataSetChanged();
                })
                .setNeutralButton("Cancel", null)
                .show();
    }
}

