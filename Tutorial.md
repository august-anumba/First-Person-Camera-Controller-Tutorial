# First Person Camera Controller Tutorial

This tutorial shows how to create a first person camera controller in Unity.

In your chosen scene, create a new C# script by right clicking and selecting Create then C# Script.

We will be naming this new C# Script `PlayerCam`.

We start my making two floats for the X and Y sensitivity, we do this by writing the following two lines of code:

```.cs
    public float sensX;
    public float sensY;
```
We will also a trasform for the players orientation in addition to two more floats for the X and Y rotation of the camera, as seen below:

```.cs
    public Transform orientation;

    float xRotation;
    float yRotation;
```
Before actually implimenting the rotation we need to then move into `void Start()` and ensure that that cursor stays locked to the middle of the screen and also invisible. We write the following lines of code under `void Start()`:

```.cs
    Cursor.lockState = CursorLockMode.Locked;
    Cursor.visible = false;
```
Now in `void Update()` we get the mouse input on both the X and Y and mulitiply it by the sensitivity float we created earlier:

```.cs
    float mouseX = Input.GetAxisRaw("Mouse X") * Time.deltaTime * sensX;
    float mouseY = Input.GetAxisRaw("Mouse Y") * Time.deltaTime * sensY;
```
Then we add the X input to the Y rotation and substract the Y input from the X rotation:

```.cs
    yRotation += mouseX;
    xRotation -= mouseY;
```
While the code above may seem confusing at first, this is the way unity handles rotations and inputs.
Now we want to make sure that the player can't look up or down past 90 degrees as this is unrealistic, we achive this by clamping the X rotation from -90f, 90f:

```.cs
    xRotation = Mathf.Clamp(xRotation, -90f, 90f);
```
To then be granted the abilty to rotate the camera around both axises and the player around the Y axis we use `Quaterion.Euler`:




-------------------------------

```.cs

```
