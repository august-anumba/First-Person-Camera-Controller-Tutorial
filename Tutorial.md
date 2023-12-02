# First Person Camera Controller Tutorial

This tutorial shows how to create a first person camera controller in Unity.

## 1. Creating `PlayerCam` C# Script

In your chosen scene, create a new C# script by right clicking and selecting `Create` then `C# Script`.

![image](https://github.com/august-anumba/First-Person-Camera-Controller-Tutorial/assets/146851823/370bb5c7-007c-40b0-b1c2-0bfe649d0440)

We will be naming this new C# Script `PlayerCam`.

We start my making two floats for the `X` and `Y` sensitivity, we do this by writing the following two lines of code:

```.cs
    public float sensX;
    public float sensY;
```
We will also use `Transform` for the players orientation in addition to two more floats for the `X` and `Y` rotation of the camera, as seen below:

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
Now in `void Update()` we get the mouse input on both the `X` and `Y` and mulitiply it by the sensitivity float we created earlier:

```.cs
    float mouseX = Input.GetAxisRaw("Mouse X") * Time.deltaTime * sensX;
    float mouseY = Input.GetAxisRaw("Mouse Y") * Time.deltaTime * sensY;
```
Then we add the `X` input to the `Y` rotation and substract the `Y` input from the `X` rotation:

```.cs
    yRotation += mouseX;
    xRotation -= mouseY;
```
While the code above may seem confusing at first, this is the way unity handles `rotations` and `inputs`.
Now we want to make sure that the player can't look up or down past 90 degrees as this is unrealistic, we achive this by clamping the `X` rotation from `-90f, 90f`:

```.cs
    xRotation = Mathf.Clamp(xRotation, -90f, 90f);
```
To then be granted the abilty to rotate the camera around both the `X` and `Y` axises and the player around only the `Y` axis we use `Quaterion.Euler` alongside `Transform` and `Orientation`:

```.cs
    transform.rotation = Quaternion.Euler(xRotation, yRotation, 0);
    orientation.rotation = Quaternion.Euler(0, yRotation, 0);
```
Your finished C# Script for `PlayerCam` should look like this:

```.cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerCam : MonoBehaviour
{
    public float sensX;
    public float sensY;

    public Transform orientation;

    float xRotation;
    float yRotation;

    void Start()
    {
        Cursor.lockState = CursorLockMode.Locked;
        Cursor.visible = false;

    }

    void Update()
    {
        float mouseX = Input.GetAxisRaw("Mouse X") * Time.deltaTime * sensX;
        float mouseY = Input.GetAxisRaw("Mouse Y") * Time.deltaTime * sensY;

        yRotation += mouseX;
        xRotation -= mouseY;

        xRotation = Mathf.Clamp(xRotation, -90f, 90f);

        transform.rotation = Quaternion.Euler(xRotation, yRotation, 0);
        orientation.rotation = Quaternion.Euler(0, yRotation, 0);
    }
}

```
## 2. Unity Player GameObject set-up

We now need to connect various GameObjects together as parent and children as well as attaching the C# Script we just created to a GameObject along with future C# Scripts to other GameObjects.

Firstly create 4 empty GameObjects and 1 Capsule by right clicking inside the `Hierachy` tab, located by default in the top left, to create and empty GameObject select `Create Empty`. To create a Capsule you simply select 3D Object and select Capsule

![image](https://github.com/august-anumba/First-Person-Camera-Controller-Tutorial/assets/146851823/8f1dd6c7-cc2f-4319-a975-dcd3bd5f0aee)





-------------------------------

```.cs

```
