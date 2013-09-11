package ro.jtonic.handsonrest.api;

import com.wordnik.swagger.annotations.Api;
import com.wordnik.swagger.annotations.ApiError;
import com.wordnik.swagger.annotations.ApiErrors;
import com.wordnik.swagger.annotations.ApiOperation;
import com.wordnik.swagger.annotations.ApiParam;

import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;
import javax.ws.rs.Produces;

/**
 * Created by jtonic on 9/11/13.
 */
@Path("cars")
@Api(value = "/cars", description = "Cars API")
@Produces("application/json")
public interface ICarResource {

    @GET
    @Path("{carId}")
    @ApiOperation(value = "Find car by ID", notes = "To get car info by car's Id",
            responseClass = "ro.jtonic.handsonrest.model.Car")
    @ApiErrors(value = {
            @ApiError(code = 400, reason = "Invalid ID supplied"),
            @ApiError(code = 404, reason = "Car not found")
    })
    public ro.jtonic.handsonrest.model.Car getCarById(
            @ApiParam(value = "ID of car that needs to be fetched", allowableValues = "range[1,10]",
                    required = true) @PathParam("carId") String carId);

}
