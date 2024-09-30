#new
def manage_teacher(request):
    if request.method == "POST" and 'delete_teacher_id' in request.POST:
        teacher_id = request.POST.get('delete_teacher_id')
        try:
            teacher = Teacher.objects.get(id=teacher_id)
        except Teacher.DoesNotExist:
            return render(request, 'manage_teacher.html', {'error': 'Teacher not found'})

        user = teacher.user
        
        try:
            if user and user.id:  # Ensure the user has a valid ID
                teacher.delete()  # Delete the Teacher first
                user.delete()  # Then delete the associated CustomUser
            else:
                # Handle the case where the user is None or has no valid ID
                return render(request, 'admin_dashboard.html', {'error': 'The associated user could not be deleted.'})
        except Exception as e:
            # Handle any errors that occur during deletion
            return render(request, 'manage_teacher.html', {'error': str(e)})

        messages.success(request, 'Teacher deleted successfully')
        return redirect('administrator:manage_teacher')  # Redirect back to the teacher list after deletion

    teachers = Teacher.objects.select_related('user').all()
    return render(request, 'manage_teacher.html', {'teachers': teachers})
