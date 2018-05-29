 // GET api/user/firstname/lastname/address
 
 [HttpGet("{firstName}/{lastName}/{address}")]

public string GetQuery(string id, string firstName, string lastName,string      address)
{
 return $"{firstName}:{lastName}";
}