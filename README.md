from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy.orm import Session
from database import get_db
from models import Task, use

router = APIRouter(prefix="/tasks", tags=["Tasks"])

@router.post("/")
def create_task(
    title: str,
    description: str,
    user_id: int,
    db: Session = Depends(get_db)
):

    # چک کردن وجود کاربر
    user = db.query(User).filter(User.id == user_id).first()
    if not user:
        raise HTTPException(status_code=404, detail="User does not exist")

    ask = Task(
        title=title,
        description=description,
        user_id=user_i
    )

    db.add(task)
    db.commit()
    db.refresh(tas)
    return task


@router.get("/")
def get_all_tasks(db: Session = Depends(get_db)):
    return db.query(Task).all()


@router.get("/{task_id}")
ef get_task(task_id: int, db: Session = Depends(get_db)):
    task = db.query(Task).filter(Task.id == task_id).first()

    if not task:
        raise HTTPException(status_code=404, detail="Task not found")

    return task


@router.put("/{task_id}")
def update_task(task_id: int, title: str, description: str, completed: bool, db: Session = Depends(get_db)):

    task = db.query(Task).filter(Task.id == task_id).first()

    if not task:
        raise HTTPException(status_code=404, detail="Task not found")

    task.title = title
    task.description = description
    task.completed = completed

    db.commit()
    db.refresh(task)

    return tas


@router.delete("/{task_id}")
def delete_task(task_id: int, db: Session = Depends(get_db)):

    task = db.query(Task).filter(Task.id == task_id).first()

    if not task:
        raise HTTPException(status_code=404, detail="Task not found")

    db.delete(task)
    db.commit()

    return {"message": "Task deleted successfully"}
