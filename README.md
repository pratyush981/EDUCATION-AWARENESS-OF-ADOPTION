# EDUCATION-AWARENESS-OF-ADOPTION


// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract EducationPlatform {
    address public owner;

    struct Course {
        string title;
        string description;
        uint256 price;
        address teacher;
        mapping(address => bool) students;
    }

    Course[] public courses;

    event CourseCreated(uint256 indexed courseId, string title, string description, uint256 price, address teacher);
    event CourseEnrolled(uint256 indexed courseId, address student);

    constructor() {
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Only owner can perform this action");
        _;
    }

    function createCourse(string memory _title, string memory _description, uint256 _price) public onlyOwner {
        courses.push(Course({
            title: _title,
            description: _description,
            price: _price,
            teacher: msg.sender
        }));
        emit CourseCreated(courses.length - 1, _title, _description, _price, msg.sender);
    }

    function enroll(uint256 _courseId) public payable {
        require(_courseId < courses.length, "Invalid course ID");
        Course storage course = courses[_courseId];
        require(msg.value == course.price, "Incorrect payment amount");
        require(!course.students[msg.sender], "Already enrolled");
        course.students[msg.sender] = true;
        emit CourseEnrolled(_courseId, msg.sender);
    }

    function getCourse(uint256 _courseId) public view returns (string memory, string memory, uint256, address) {
        require(_courseId < courses.length, "Invalid course ID");
        Course memory course = courses[_courseId];
        return (course.title, course.description, course.price, course.teacher);
    }
}
