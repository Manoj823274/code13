package com.example.usermvc.model;

public class User {
    private Integer id;
    private String name;
    private String email;
    private String phno;

    // Constructors, getters, setters
}
package com.example.usermvc.repository;

import com.example.usermvc.model.User;
import java.util.*;

public class UserRepository {
    private static List<User> users = new ArrayList<>();
    private static int idCounter = 1;

    public static void addUser(User user) {
        user.setId(idCounter++);
        users.add(user);
    }

    public static List<User> getAllUsers() {
        return users;
    }

    public static User getUserById(int id) {
        return users.stream().filter(u -> u.getId() == id).findFirst().orElse(null);
    }

    public static void updateUser(User updatedUser) {
        users.replaceAll(u -> u.getId() == updatedUser.getId() ? updatedUser : u);
    }

    public static void deleteUser(int id) {
        users.removeIf(u -> u.getId() == id);
    }
}
package com.example.usermvc.controller;

import com.example.usermvc.model.User;
import com.example.usermvc.repository.UserRepository;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;

@Controller
public class UserController {

    @GetMapping("/")
    public String home(Model model) {
        model.addAttribute("user", new User());
        return "index";
    }

    @PostMapping("/save")
    public String saveUser(@ModelAttribute User user) {
        UserRepository.addUser(user);
        return "redirect:/users";
    }

    @GetMapping("/users")
    public String viewUsers(Model model) {
        model.addAttribute("userList", UserRepository.getAllUsers());
        return "users";
    }

    @GetMapping("/edit/{id}")
    public String editUser(@PathVariable int id, Model model) {
        model.addAttribute("user", UserRepository.getUserById(id));
        return "edit_user";
    }

    @PostMapping("/update")
    public String updateUser(@ModelAttribute User user) {
        UserRepository.updateUser(user);
        return "redirect:/users";
    }

    @GetMapping("/delete/{id}")
    public String deleteUser(@PathVariable int id) {
        UserRepository.deleteUser(id);
        return "redirect:/users";
    }
}
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head><title>User Form</title></head>
<body>
<h2>User Form</h2>
<form th:action="@{/save}" th:object="${user}" method="post">
    Name: <input type="text" th:field="*{name}"/><br>
    Email: <input type="text" th:field="*{email}"/><br>
    Phno: <input type="text" th:field="*{phno}"/><br>
    <button type="submit">Save</button>
</form>
<a th:href="@{/users}">View All Users</a>
</body>
</html>
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head><title>All Users</title></head>
<body>
<h2>View All Users Here</h2>
<a th:href="@{/}">+ Add New User</a>
<table border="1">
    <tr><th>Id</th><th>Name</th><th>Email</th><th>Phno</th><th>Action</th></tr>
    <tr th:each="u : ${userList}">
        <td th:text="${u.id}"></td>
        <td th:text="${u.name}"></td>
        <td th:text="${u.email}"></td>
        <td th:text="${u.phno}"></td>
        <td>
            <a th:href="@{/edit/{id}(id=${u.id})}">Edit</a>
            <a th:href="@{/delete/{id}(id=${u.id})}">Delete</a>
        </td>
    </tr>
</table>
</body>
</html>
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head><title>Edit User</title></head>
<body>
<h2>Edit User</h2>
<form th:action="@{/update}" th:object="${user}" method="post">
    <input type="hidden" th:field="*{id}"/>
    Name: <input type="text" th:field="*{name}"/><br>
    Email: <input type="text" th:field="*{email}"/><br>
    Phno: <input type="text" th:field="*{phno}"/><br>
    <button type="submit">Update</button>
</form>
</body>
</html>
spring.mvc.view.prefix=/templates/
spring.mvc.view.suffix=.html


