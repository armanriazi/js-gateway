import { server } from '../../../src/app.js'
import supertest from 'supertest'
const request = supertest(server)
import { randomUUID } from 'crypto'

import {
  adminPassword,
  mockPassword,
} from '../data/user.js'
import usersService from '../requiredTest/userService.js'
import messages from '../../../src/common/messages.js'



export const userGet = () => {
  describe('GET USER ', () => {
    let adminInfo
    let userId
    let token
    let superAdminId
    let user

    beforeAll(async () => {
      try {
        adminInfo = {
          username: randomUUID(),
          password: adminPassword,
          role: 'superAdmin'
        }

        superAdminId = await usersService.create(adminInfo)

        delete adminInfo.role

        token = await usersService.login(adminInfo)

        user = {
          username: randomUUID(),
          password: mockPassword,
          role: 'user',
        }

        userId = await usersService.create(user)
      } catch (error) {
        console.log(error)
      }
    })

    afterAll(async () => {
      await usersService.delete(userId)
      await usersService.delete(superAdminId)
    })

    it('user has been found', (done) => {
      try {
        request
          .get(`/api/users/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: expect.arrayContaining([
                  expect.objectContaining({
                    id: userId,
                    role: user.role
                  })
                ]),
              })
            )
            return done()
          })
      } catch (error) {}
    })

    // it('userId is not uuid', (done) => {
    //   try {
    //     const wrongUserId = 'kjh23j4h2jh34'

    //     request
    //       .get(`/api/users/${wrongUserId}`)
    //       .set('Authorization', `Bearer ${token}`)
    //       .expect(500)

    //       .end(function (err, res) {
    //         if (err) return done(err)
    //         expect(res.body.data).toEqual(
    //           expect.objectContaining({
    //             status: 'error',
    //             message: `${messages.m.SERVER_ERROR}`,
    //           })
    //         )
    //         return done()
    //       })
    //   } catch (error) {}
    // })

    it('token is expired', (done) => {
      try {
        const expiredToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjNhZjZhZWIyLTg3M2EtNDNiNS04ZDM0LTE0MzczZGE2ZTFiMSIsImlhdCI6MTY2NjUwNjk3MH0.o1k__1jtkL3C2Kte'

        request
          .get(`/api/users/${userId}`)
          .set('Authorization', `Bearer ${expiredToken}`)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.TOKEN_EXPIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('login needed', (done) => {
      try {
        request
          .get(`/api/users/${userId}`)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.LOGIN_NEEDED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })
  })
}
