# First Person Camera Controller Tutorial

This tutorial shows how to create a first person camera controller in Unity.

In your chosen scene, create a new C# script by right clicking and selecting Create then C# Script.

We will be naming this new C# Script `PlayerCam`.

We start my making two floats for the X and Y sensitivity, we do this by writing the following two lines of code:

```.cs
    public float sensX;
    public float sensY;
```
We will also add a trasform for the players orientation in addition to two more floats for the X and Y rotation of the camera, as seen bellow:

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
Now in `void Update()` we get the mouse input on both the X and Y using:

```.cs
    float mouseX = Input.GetAxisRaw("Mouse X") * Time.deltaTime * sensX;
    float mouseY = Input.GetAxisRaw("Mouse Y") * Time.deltaTime * sensY;
```





-------------------------------

```.cs

```
