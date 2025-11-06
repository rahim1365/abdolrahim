import unitte
from tasks import TaskManager

class TestTaskManager(unittest.TestCase):
    def test_add_and_list_tasks(self):
        manager = TaskManager()
        manager.add_task("Test Task")
        self.assertEqual(len(manager.tasks), 1)
        self.assertEqual(manager.tasks[0].title, "Test Task")

    f test_mark_done(self):
        manager = TaskManager()
        manager.add_task("Do homework")
        manager.mark_done(1)
        self.assertTrue(manager.tasks[0].done)

    def test_delete_task(self):
        manager = TaskManager()
        manager.add_task("Temporary")
        manager.delete_task(1)
        self.assertEqual(len(manager.tasks), 0)

if __name__ == "__main__":
    unittest.main()
