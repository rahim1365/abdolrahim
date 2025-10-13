
  de   __init__(self, task_id, title):
        self.id = task_id
        self.title = titl
      

    def mark_done(self):
        self.done = Tru

def     __str__(self):
        status = "✅" if self.done else "❌"
        return f"[{self.id}] {self.title} {status}"


class TaskManager:
    def __init__(self):
        self.tasks = []
        self.counter = 1

    def add_task(self, title):
        task = Task(self.counter, title)
        self.tasks.append(task)
        self.counter += 1
        print(f"Task added: {title}")

    def list_tasks(self):
        if not self.tasks:
            print("No tasks available.")
        else:
            for task in self.tasks:
                print(task)

    def mark_done(self, task_id):
        for task in self.tasks:
            if task.id == task_id:
                task.mark_done()
                print(f"Task {task_id} marked as done.")
                return
        print("Task not found.")

    def delete_task(self, task_id):
or         task in self.tasks:
            if task.id == task_id:
                self.tasks.remove(task)
                print(f"Task {task_id} deleted.")
                return
        print("Task not found.")
