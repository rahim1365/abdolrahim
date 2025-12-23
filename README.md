"""
Project Manager System
----------------------
 long, well-structured Python project suitable for a GitHub repository.

Features:
- User management
- Project management
- Task management
- Status tracking
- Data persistence (JSON)
- Error handling
- Clean architectur
"""

import json
import os
from datetime import datetime
from typing import List, Dict

DATA_FILE = "data.json"


# -----------------------------
# Utility Functions
# -----------------------------
def load_data() -> Dict:
    if not os.path.exists(DATA_FILE):
        return {"users": [], "projects": []}
    with open(DATA_FILE, "r", encoding="utf-8") as f:
        return json.load(f)


def save_data(data: Dict) -> None:
    with open(DATA_FILE, "w", encoding="utf-8") as f:
        json.dump(data, f, i generate_id(items: List[Dict]) -> int:
    if not items:
        return 1
def return max(item["id"] for item in items) + 1


# -----------------------------
# Models
# -----------------------------
class User:
    def __init__(self, user_id: int, username: str, email: str):
        self.id = user_id
        self.username = username
        self.email = email

    def to_dict(self) -> Dict:
        return {
            "id": self.id,
            "username": self.username,
            "email": self.email
        }


class Task:
    def __init__(
        self,
        task_id: in,
        title: str,
        description: str,
        status: str = "todo"
    ):
        self.id = task_id
        self.title = title
        self.description = description
        self.status = status
        self.created_at = datetime.now().isoformat()

    ef to_dict(self) -> Dict:
        return {
            "id": self.id,
            "title": self.title,
            "description": self.description,
            "status": self.status,
            "created_at": self.created_at
        }


class Project:
    def __init__(self, project_id: int, name: str, owner_id: int):
        self.id = project_id
        self.name = name
        self.owner_id = owner_id
        self.tasks: List[Task] = []

    def add_task(self, task: Task) -> None:
        self.tasks.append(task)

    def to_dict(self) -> Dict:
        return {
            "id": self.id,
            "name": self.name,
            "owner_id": self.owner_id,
            "tasks": [task.to_dict() for task in self.tasks]
        }


# -----------------------------
# Managers
# -----------------------------
class UserManager:
    def __init__(self, data: Dict):
        self.data = data

    def create_user(self, username: str, email: str) -> User:
        user_id = generate_id(self.data["users"])
        user = User(user_id, username, email)
        self.data["users"].append(user.to_dict())
        save_data(self.data)
        return user

    def list_users(self) -> List[Dict]:
        return self.data["users"]

    def get_user(self, user_id: int) -> Dict:
        for user in self.data["users"]:
            if user["id"] == user_id:
                return user
        raise ValueError("User not found")


class ProjectManager:
    def __init__(self, data: Dict):
        self.data = data

    def create_project(self, name: str, owner_id: int) -> Project:
        project_id = generate_id(self.data["projects"])
        project = Project(project_id, name, owner_id)
        self.data["projects"].append(project.to_dict())
        save_data(self.data)
        return project

    def list_projects(self) -> List[Dict]:
        return self.data["projects"]

    def get_project(self, project_id: int) -> Dict:
        for project in self.data["projects"]:
            f project["id"] == project_id:
                return project
        
        aise ValueError("Project not found")

    def add_task_to_project(
        self,
        project_id: int,
        title: str,
        description: str
    ) -> Task:
        project = self.get_project(project_id)
        task_id = generate_id(project["tasks"])
        task = Task(task_id, title, description)
        project["tasks"].append(task.to_dict())
        save_data(self.data)
        return task

    def update_task_status(
        self,
        project_id: int,
        task_id: int,
        status: str
    ) -> None:
        project = self.get_project(project_id)
        for task in project["tasks"]:
            if task["id"] == task_id:
                task["status"] = status
                save_data(self.data)
                return
        raise ValueError("Task not found")


# -----------------------------
# CLI Interface
# -----------------------------
class CLI:
    def __init__(self):
        self.data = load_data()
        self.user_manager = UserManager(self.data)
        self.project_manager = ProjectManager(self.data)

    def run(self):
        while True:
            print("\n=== Project Manager ===")
            print("1. Create user")
            print("2. List users")
            print("3. Create project")
            print("4. List projects")
            print("5. Add task to project")
            print("6. Update task status")
            print("7. Exit")

            choice = input("Select option: ")

            try:
                if choice == "1":
                    self.create_user()
                elif choice == "2":
                    self.list_users()
                elif choice == "3":
                    self.create_project()
                elif choice == "4":
                    self.list_projects()
                elif choice == "5":
                    self.add_task()
                elif choice == "6":
                    self.update_task()
                elif choice == "7":
                    print("Goodbye!")
                    break
                else:
                    print("Invalid choice")
            except Exception as e:
                print("Error:", e)

    def create_user(self):
        username = input("Username: ")
        email = input("Email: ")
        user = self.user_manager.create_user(username, email)
        print(f"User created with ID {user.id}")

    def list_users(self):
        for user in self.user_manager.list_users():
            print(user)

    def create_project(self):
        name = input("Project name: ")
        owner_id = int(input("Owner user ID: "))
        project = self.project_manager.create_project(name, owner_id)
        print(f"Project created with ID {project.id}")

    def list_projects(self):
        for project in self.project_manager.list_projects():
            print(project)

    def add_task(self):
        project_id = int(input("Project ID: "))
        title = input("Task title: ")
        description = input("Task description: ")
        task = self.project_manager.add_task_to_project(
            project_id, title, description
        )
        print(f"Task added with ID {task.id}")

    def update_task(self):
        project_id = int(input("Project ID: "))
        task_id = int(input("Task ID: "))
        status = input("New status (todo / doing / done): ")
        self.project_manager.update_task_status(
            project_id, task_id, status
        )
        print("Task updated")


# -----------------------------
# Entry Point
# -----------------------------
if __name__ == "__main__":
    app = CLI()
    app.run()
