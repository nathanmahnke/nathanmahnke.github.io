---
layout: page
title: Database GUI
description: GUI written in C# to interact with SQL Server
img: assets/img/DatabaseGUI.png
importance: 3
category: Professional
---

This project is as a simple database connector and fully functional graphical user interface (GUI) tailored for managing the details of a baseball league’s data. Built in C#, the application interfaces with a SQL Server instance to display and manipulate information stored across multiple relational tables—including teams, players, and their associated attributes.

The application allows users to seamlessly view, sort, insert, and delete records through an intuitive GUI. Notably, deletion operations were designed with cascading logic, enabling associated entries to be cleaned up in a single transaction. While suitable for a controlled development environment, this behavior is flagged for revision in any production deployment to ensure data integrity and user oversight.

Regarding security, the project currently opens a prompt window requesting the SQL Server connection string. This avoids displaying it in plain text within the code, but has the added time cost of inputting every time. In a production setting, this could be upgrade by securely storing the connection string in an encrypted configuration file or leveraging an operating system’s secure credential storage (such as Windows Credential Manager or Azure Key Vault).

Behind the scenes, the system uses DAO (Data Access Object) classes such as PlayerDAO and TeamDAO to abstract database interactions. These classes handle the orchestration of queries, the mapping of SQL results into C# objects, and the dynamic toggling of sort order via simple user interactions. For example, PlayerDAO not only retrieves all player entries but also enables sorting by any column and manages graceful handling of nullable fields.

The program demonstrates robust connection management, error handling, and parameterized queries to prevent SQL injection. Its modular structure lays the foundation for future expansion—such as additional reporting features or authentication layers.

You can explore the source code [here](https://github.com/nathanmahnke/DatabaseGUIMahnke/)
