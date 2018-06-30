# Programas-FGC2018
/* Copyright (c) 2017 FIRST. All rights reserved.
 *
 * Redistribution and use in source and binary forms, with or without modification,
 * are permitted (subject to the limitations in the disclaimer below) provided that
 * the following conditions are met:
 *
 * Redistributions of source code must retain the above copyright notice, this list
 * of conditions and the following disclaimer.
 *
 * Redistributions in binary form must reproduce the above copyright notice, this
 * list of conditions and the following disclaimer in the documentation and/or
 * other materials provided with the distribution.
 *
 * Neither the name of FIRST nor the names of its contributors may be used to endorse or
 * promote products derived from this software without specific prior written permission.
 *
 * NO EXPRESS OR IMPLIED LICENSES TO ANY PARTY'S PATENT RIGHTS ARE GRANTED BY THIS
 * LICENSE. THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
 * "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO,
 * THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
 * ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE
 * FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
 * DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
 * SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
 * CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
 * OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
 * OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
 */

package org.firstinspires.ftc.teamcode;

import com.qualcomm.robotcore.eventloop.opmode.Disabled;
import org.firstinspires.ftc.robotcore.external.Drive;
import com.qualcomm.robotcore.eventloop.opmode.OpMode;
import com.qualcomm.robotcore.eventloop.opmode.TeleOp;
import com.qualcomm.robotcore.hardware.DcMotor;
import com.qualcomm.robotcore.util.ElapsedTime;
import com.qualcomm.robotcore.util.Range;

@TeleOp(name="OmniTriple", group="Iterative Opmode")

public class OmniTriple extends OpMode
{
    // Declare OpMode members.
    private ElapsedTime runtime = new ElapsedTime();
    private DcMotor leftDrive = null;
    private DcMotor rightDrive = null;
    private DcMotor centreDrive = null;
    private DcMotor elevadorDrive = null;
    private CRServo cajas = null;


    //Code to run ONCE when the driver hits INIT
    @Override
    public void init() {
        telemetry.addData("Status", "Initialized");

        leftDrive  = hardwareMap.get(DcMotor.class, "leftMotor");
        rightDrive = hardwareMap.get(DcMotor.class, "rightMotor");
        centreDrive = hardwareMap.get(DcMotor.class, "centreMotor");
        elevadorDrive = hardwareMap.get(DcMotor.class, "motor");
        cajas = hardwareMap.get(CRServo.class, "cajas");

        leftDrive.setDirection(DcMotor.Direction.REVERSE);
        rightDrive.setDirection(DcMotor.Direction.FORWARD);
        centreDrive.setDirection(DcMotor.Direction.FORWARD);

        telemetry.addData("Status", "Initialized");
    }

    //Code to run REPEATEDLY after the driver hits INIT, but before they hit PLAY
    @Override
    public void init_loop() {
    }

    //Code to run ONCE when the driver hits PLAY
    @Override
    public void start() {
        runtime.reset();
    }

    //Code to run REPEATEDLY after the driver hits PLAY but before they hit STOP
    @Override
    public void loop() {
        double leftPower;
        double rightPower;
        double centrePower;
        double elevadorPower;
        double cajasPower;

        // POV Mode uses left stick to go forward, and right stick to turn.
        double drive = -gamepad1.left_stick_y;
        double turn  =  gamepad1.left_stick_x;
        leftPower    = Range.clip(drive + turn, -1.0, 1.0) ;
        rightPower   = Range.clip(drive - turn, -1.0, 1.0) ;
        centrePower = gamepad1.right_stick_x;

        // Tank Mode uses one stick to control each wheel.
        /*leftPower  = -gamepad1.left_stick_y ;
        rightPower = -gamepad1.left_stick_x ;*/

        // Control power of wheels.
        if (!gamepad1.right_bumper) {
          leftPower = leftPower * 0.75;
          rightPower = rightPower * 0.75;
          centrePower = centrePower * 0.75;
        }

        // Move the lift.
        if (gamepad1.dpad_up) {
          centrePower = 0.5;
        } else if (gamepad1.dpad_down) {
          centrePower = -0.5
        } else {
          centrePower = 0;
        }

        // Activate the movement of the mechanism to pick boxes.
        boolean click;
        if (gamepad1.left_bumper) {
          click = true;
        } else if (!gamepad1.left_bumper && click) {
          if (cajasPower == 0) {
            cajasPower = 1;
          } else {
            cajasPower == 0;
          }
          click = false;
        }

        // Send calculated power to wheels
        leftDrive.setPower(leftPower);
        rightDrive.setPower(rightPower);
        centreDrive.setPower(centrePower);
        elevadorDrive.setPower(elevadorPower);
        cajas.setPower(cajasPower);

        // Show the elapsed game time and wheel power.
        telemetry.addData("Status", "Run Time: " + runtime.toString());
        telemetry.addData("Motors", "left (%f), right (%f)", leftPower, rightPower);
        telemetry.addData("SitckX", gamepad1.right_stick_x);
    }

    //Code to run ONCE after the driver hits STOP
    @Override
    public void stop() {
    }

}